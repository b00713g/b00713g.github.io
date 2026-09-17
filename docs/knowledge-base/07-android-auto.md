# 7. Android Automotive

AAOS runs natively on head units and has direct access to vehicle signals via the Vehicle HAL. Compromise the IVI and you may reach CAN/Ethernet.

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
