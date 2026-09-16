# 2. Vehicle Networks & Diagnostics

Beyond CAN: LIN, FlexRay, Automotive Ethernet, and the diagnostic protocols layered on top.

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
