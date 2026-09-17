# 7. Android Automotive

AAOS runs natively on head units and has direct access to vehicle signals via the Vehicle HAL. Compromise the IVI and you may reach CAN/Ethernet.

## Try it first

### ADB — Explore vehicle signals on the AAOS emulator

```bash
# Install the AAOS emulator image in Android Studio:
# SDK Manager → System Images → Automotive with Google APIs

# Start the emulator, then:
adb shell

# List all vehicle properties the OS exposes
dumpsys car_service | grep "property:"

# Read specific vehicle signals
cmd car_service get-property 0x11400400    # PERF_VEHICLE_SPEED
cmd car_service get-property 0x11600404    # GEAR_SELECTION
cmd car_service get-property 0x11600505    # PARKING_BRAKE_ON

# List what permissions third-party apps can request
pm list permissions | grep "car\|vehicle\|vhal"

# Check which apps have vehicle signal access
dumpsys package | grep -B5 "android.car.permission"
```

**What you're seeing:** the Vehicle HAL exposes real powertrain signals to the OS layer. A malicious AAOS app with the right permissions can read speed, gear, location, and in some implementations, write to HVAC or door locks.

### Burp / mitmproxy — MITM a vehicle companion app

Most OEMs have a companion app (Tesla, FordPass, myVW, Rivian, etc.) that talks to cloud APIs which talk to the vehicle. Intercept that traffic:

```bash
# Option A: mitmproxy (free, CLI)
pip install mitmproxy
mitmproxy --listen-port 8080

# Option B: Burp Suite Community (GUI)
# Download from portswigger.net

# On your Android device/emulator:
# 1. Set Wi-Fi proxy to your_machine_ip:8080
# 2. Install the CA cert:
#    - mitmproxy: visit mitm.it on the device browser
#    - Burp: visit burp/ on the device browser
# 3. On Android 7+, user certs aren't trusted by default.
#    Either root the device or use Frida to bypass (see below)

# 4. Open the companion app, log in, interact with features
# 5. Watch the API calls in your proxy:

# Common findings:
# - API endpoints exposing vehicle location, VIN, telemetry
# - Bearer tokens with excessive scope or long expiration
# - Missing rate limiting on lock/unlock endpoints
# - Vehicle commands (start, lock, honk) as simple POST requests
# - Lack of certificate pinning (or weak pinning you can bypass)
```

**What you're seeing:** the full API surface between a user's phone and their vehicle. If you can intercept this traffic, you can map every command the app can send to the car and look for authorization flaws.

### Frida — Hook a running app at runtime

Frida lets you inject JavaScript into a running Android process. Essential for inspecting what an app does internally:

```python
# pip install frida-tools

# List processes on the connected device
frida-ps -U | grep -i "vehicle\|ford\|tesla\|vw"

# Hook a function — example: intercept all HTTP requests
frida -U -n com.oem.companion.app -l hook.js
```

```javascript
// hook.js — log every URL the app requests
Java.perform(function() {
    var URL = Java.use("java.net.URL");
    URL.$init.overload("java.lang.String").implementation = function(url) {
        console.log("[URL] " + url);
        return this.$init(url);
    };

    // Hook OkHttp (most Android apps use it)
    var OkHttpClient = Java.use("okhttp3.OkHttpClient");
    var Builder = Java.use("okhttp3.Request$Builder");
    Builder.url.overload("java.lang.String").implementation = function(url) {
        console.log("[OkHttp] " + url);
        return this.url(url);
    };
});
```

**What you're seeing:** every API call the app makes, including ones hidden behind UI flows you haven't triggered yet. You'll find staging endpoints, debug APIs, and sometimes authentication tokens logged in plaintext.

### Certificate pinning bypass with Frida

Most vehicle companion apps pin their TLS certificates so Burp/mitmproxy can't intercept. Bypass it:

```bash
# Option 1: Universal SSL pinning bypass script
frida -U -n com.oem.companion.app \
  -l https://raw.githubusercontent.com/NVISOsecurity/\
disable-flutter-tls-verification/main/disable-flutter-tls.js

# Option 2: objection (automated Frida wrapper)
pip install objection
objection -g com.oem.companion.app explore
# Inside objection:
> android sslpinning disable

# Option 3: For specific pinning implementations
# Hook the certificate validation method and force it to pass:
```

```javascript
// bypass_pinning.js
Java.perform(function() {
    // OkHttp CertificatePinner
    var CertPinner = Java.use("okhttp3.CertificatePinner");
    CertPinner.check.overload("java.lang.String", "java.util.List")
        .implementation = function(hostname, peerCerts) {
            console.log("[Pinning bypass] " + hostname);
            return;  // skip the check
        };

    // TrustManager — accept all certs
    var TrustManager = Java.registerClass({
        name: "com.bypass.TrustManager",
        implements: [Java.use("javax.net.ssl.X509TrustManager")],
        methods: {
            checkClientTrusted: function(chain, authType) {},
            checkServerTrusted: function(chain, authType) {},
            getAcceptedIssuers: function() { return []; }
        }
    });
});
```

**What you're seeing:** once pinning is bypassed, all HTTPS traffic between the app and the vehicle cloud flows through your proxy. Now you can test every API endpoint, replay commands, and look for IDOR/authorization flaws on vehicle control APIs.

---

## Read first

- [icanhack.nl — Infotainment & Telematics Research](https://icanhack.nl/knowledge-base/existing-research/infotainment-telematics/)
- [Security Analysis of Android Automotive (Pese et al., 2020)](https://www.researchgate.net/publication/340632296) — first systematic AAOS security analysis
- [HARNESS: Vehicle Control Protection on Untrusted AAOS (USENIX Security 2025)](https://www.usenix.org/conference/usenixsecurity25)
- [A Survey of Security Vulnerabilities in Android Automotive Apps](https://www.researchgate.net/publication/365894783)
- [Analyzing Privacy Implications of Data Collection in AAOS](https://arxiv.org/pdf/2409.15561)
- [AAOS Security Bulletins](https://source.android.com/docs/security/bulletin/aaos)
- [Advanced Frida Usage Part 1 — iOS Encryption Libraries (8kSec)](https://8ksec.io/advanced-frida-usage-part-1-ios-encryption-libraries-8ksec-blogs/) — Frida techniques transfer directly to AAOS app instrumentation
- [Illustrated TLS Connections (tls13.xargs.org)](https://tls13.xargs.org/) — useful for understanding ISO 15118 TLS and AAOS backend comms

## Key concepts

- **AAOS vs. Android Auto** — Auto mirrors your phone. AAOS is a native vehicle OS.
- **Vehicle HAL (VHAL)** — the interface between AAOS and in-vehicle networks. This is the security boundary.
- **VehiclePropertyIds** — AAOS exposes powertrain status, HVAC, gear selection, and more to apps.
- **Third-party app risks** — malicious or vulnerable AAOS apps can leak telemetry or manipulate vehicle state.

## Mobile App & IoT Companion App Testing

These aren't AAOS-specific but the techniques transfer directly to automotive companion apps (Tesla, FordPass, myVW, Rivian, etc.):

- [Intercepting Mobile Application Traffic with Caido and Frida (Matt Brown)](https://brownfinesecurity.com/blog/intercepting-mobile-traffic-with-caido-and-frida) — proxying HTTP traffic from mobile devices
- [Attacking Enterprise IoT Mobile Apps — Auth Downgrade (Matt Brown)](https://brownfinesecurity.com/blog/attacking-enterprise-iot-mobile-apps) — authentication bypass in companion apps
- [IoT Security Fail: Missing ONVIF Authentication (Matt Brown)](https://brownfinesecurity.com/blog/iot-vulnerability-basics-onvif-missing-authentication) — remote camera control via missing auth
- [Intercepting Traffic from Police Bodycam App Sending Data to China (Matt Brown)](https://brownfinesecurity.com/blog/police-bodycam-data-to-china) — TLS certificate validation failure
- [Intro to Wireshark for IoT Pentesters (Brown Fine Security Training)](https://training.brownfinesecurity.com/l/pdp/intro-to-wireshark-for-iot-pentesters) — IoT-focused Wireshark course

## Lab

1. Set up the [AAOS emulator](https://developer.android.com/training/cars/testing) in Android Studio
2. Explore the VHAL: `adb shell dumpsys car_service`
3. Build a minimal app requesting vehicle-signal permissions
4. Intercept traffic from pre-installed apps with mitmproxy or Frida
