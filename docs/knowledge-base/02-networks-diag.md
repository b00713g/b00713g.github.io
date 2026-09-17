# 2. Vehicle Networks & Diagnostics

Beyond CAN: LIN, FlexRay, Automotive Ethernet, and the diagnostic protocols layered on top.

## Try it first

Before reading the specs, run these examples to see what these protocols actually do.

### UDS — Read ECU identity with Scapy

UDS (Unified Diagnostic Services) is how dealer tools talk to ECUs. This sends a `ReadDataByIdentifier` request to get the ECU's VIN, hardware number, and software version.

```python
from scapy.contrib.automotive.uds import *
from scapy.contrib.automotive.isotp import ISOTPSocket
import can

# Connect to an ECU via ISO-TP on virtual CAN
sock = ISOTPSocket("vcan0", tx_id=0x7E0, rx_id=0x7E8)

# Read ECU identification (service 0x22, DID 0xF190 = VIN)
resp = sock.sr1(UDS()/UDS_RDBI(identifiers=[0xF190]), timeout=2)
if resp and resp.haslayer(UDS_RDBIPR):
    print(f"VIN: {resp.dataRecord.decode('ascii', errors='ignore')}")

# Enumerate which diagnostic services are available
for svc in [0x10, 0x11, 0x22, 0x27, 0x2E, 0x31, 0x34, 0x36, 0x37, 0x3E]:
    resp = sock.sr1(UDS(service=svc), timeout=0.5, verbose=0)
    if resp and resp.service != 0x7F:  # 0x7F = negative response
        print(f"  Service 0x{svc:02X}: supported ✓")
    elif resp:
        nrc = resp.negativeResponseCode
        print(f"  Service 0x{svc:02X}: rejected (NRC 0x{nrc:02X})")
```

**What you're seeing:** service 0x22 reads data, 0x27 is security access (seed-key), 0x31 runs routines, 0x34–0x37 are firmware download. If 0x27 responds, there's an authentication bypass to find.

### DoIP — Discover ECUs over Ethernet

DoIP (Diagnostics over IP) is UDS tunneled through TCP/IP on Automotive Ethernet. This discovers which ECUs are on the network.

```python
from scapy.contrib.automotive.doip import *

# Send a Vehicle Identification Request broadcast
sock = DoIPSocket("192.168.1.255")  # broadcast on your subnet
resp = sock.sr1(DoIPVehicleIdentificationRequest(), timeout=3)

if resp and resp.haslayer(DoIPVehicleAnnouncementResponse):
    print(f"VIN:         {resp.vin.decode()}")
    print(f"Logical Addr: 0x{resp.logical_address:04X}")
    print(f"GID:         {resp.gid.hex()}")

# Once you know the ECU's IP, open a diagnostic session
doip_sock = DoIPSocket("192.168.1.10")  # ECU's IP
doip_sock.sr1(UDS()/UDS_DSC(diagnosticSessionType=0x01))  # default session
doip_sock.sr1(UDS()/UDS_RDBI(identifiers=[0xF190]))       # read VIN via DoIP
```

**What you're seeing:** DoIP broadcasts are how a tester discovers ECUs on an Ethernet segment. No authentication on the discovery layer — anyone on the network can find every ECU.

### XCP — Read ECU calibration data

XCP (Universal Measurement and Calibration Protocol) lets you read and write calibration memory in real-time. Used in development to tune engine maps, but sometimes left enabled in production.

```python
from scapy.contrib.automotive.xcp import *
from scapy.contrib.automotive.ccp import *

# XCP over CAN — connect to the ECU
sock = ISOTPSocket("vcan0", tx_id=0x551, rx_id=0x552)

# CONNECT command
resp = sock.sr1(XCPOnCAN()/XCP_Connect(mode=0x00), timeout=2)
if resp:
    print(f"XCP connected — resource: 0x{resp.resource:02X}")

# Read a memory block (e.g., calibration table at 0xA0010000)
sock.sr1(XCPOnCAN()/XCP_SetMTA(addr=0xA0010000, addrExt=0x00))
resp = sock.sr1(XCPOnCAN()/XCP_Upload(numberOfElements=64))
if resp:
    print(f"Memory dump: {resp.data.hex()}")

# If WRITE is enabled (resource bit 0x02), you can modify calibration
# data in-place — fuel maps, timing tables, torque limits
```

**What you're seeing:** XCP gives raw memory read/write on a running ECU. If the CONNECT succeeds without authentication, you have calibration-level access. This is how tuners modify ECU maps, and how an attacker could change safety-critical parameters.

---

## Read first

The [icanhack.nl Knowledge Base](https://icanhack.nl/knowledge-base/networks/introduction/) covers this comprehensively:

- [LIN Bus](https://icanhack.nl/knowledge-base/networks/lin-bus/)
- [FlexRay](https://icanhack.nl/knowledge-base/networks/flexray/)
- [Automotive Ethernet](https://icanhack.nl/knowledge-base/networks/automotive-ethernet/)
- [ISO-TP](https://icanhack.nl/knowledge-base/diagnostics/iso-tp/)
- [VW TP 2.0](https://icanhack.nl/knowledge-base/diagnostics/vw-tp20/)
- [OBD-II](https://icanhack.nl/knowledge-base/diagnostics/obd-ii/)
- [UDS](https://icanhack.nl/knowledge-base/diagnostics/uds/)
- [CCP](https://icanhack.nl/knowledge-base/diagnostics/ccp/) / [XCP](https://icanhack.nl/knowledge-base/diagnostics/xcp/)
- [Vehicle Documentation](https://icanhack.nl/knowledge-base/networks/vehicle-documentation/) — how to access OEM wiring diagrams and service portals

Additional:

- [Scapy Automotive Documentation](https://scapy.readthedocs.io/en/latest/layers/automotive.html) — UDS, DoIP, ISO-TP, SOME/IP, HSFZ, GMLAN, OBD layers
- [Nils Weiss — Automotive Network Scans with Scapy (Troopers 2022)](https://www.youtube.com/watch?v=lUfmlmFwC1A)
- [Nils Weiss — Automotive Penetration Testing with Scapy (Troopers 2019)](https://troopers.de/troopers19/agenda/znxvht/)
- [Automated Threat Evaluation of Automotive Diagnostic Protocols (Weiss et al.)](https://www.researchgate.net/publication/351483528)
- [Analysis of the DoIP Protocol for Security Vulnerabilities (arXiv)](https://arxiv.org/abs/2211.12177) — DoIP as an attack vector
- [UPTANE — Securing Automotive OTA Updates](https://uptane.github.io/)
- [AUTOSAR SOME/IP Protocol Specification](https://www.autosar.org/fileadmin/standards/R22-11/FO/AUTOSAR_PRS_SOMEIPProtocol.pdf)

## Tools

| Tool | Purpose | Link |
|---|---|---|
| scapy (automotive) | Full diagnostic stack: UDS, DoIP, HSFZ, ISO-TP, SOME/IP, GMLAN, XCP — with automated scanners | [secdev/scapy](https://github.com/secdev/scapy) |
| python-udsoncan | Clean UDS implementation for scripting | [pylessard/python-udsoncan](https://github.com/pylessard/python-udsoncan) |
| CaringCaribou | Quick UDS/XCP service enumeration | [CaringCaribou/caringcaribou](https://github.com/CaringCaribou/caringcaribou) |
| gallia | Automotive pentesting framework (Fraunhofer) | [Fraunhofer-AISEC/gallia](https://github.com/Fraunhofer-AISEC/gallia) |
| vsomeip | SOME/IP open-source stack | [COVESA/vsomeip](https://github.com/COVESA/vsomeip) |

For scripting patterns, see also [icanhack.nl — Scripting](https://icanhack.nl/knowledge-base/tools/scripting/) and [icanhack.nl — DBC Files](https://icanhack.nl/knowledge-base/tools/dbc-files/).
