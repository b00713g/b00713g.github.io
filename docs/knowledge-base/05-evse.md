# 5. EVSE & EV Charging

EV chargers are networked embedded systems with their own attack surface. They connect to vehicles via PLC and to backend systems via OCPP.

## Read first

- [icanhack.nl — EV Charging Research](https://icanhack.nl/knowledge-base/existing-research/ev-charging/)
- [V2G Injector — Dudek, Delaunay, Fargues (SSTIC 2019)](https://www.sstic.org/2019/presentation/v2g_injector_playing_with_electric_cars_and_charging_stations_via_powerline/) — PLC-layer MITM via HomePlug GreenPHY
- [Brokenwire — Wireless Disruption of CCS Charging](https://brokenwire.fail/)
- [Current Affairs: CCS EV Charging Security (USENIX Security 2025)](https://www.usenix.org/system/files/usenixsecurity25-szakaly.pdf) — real-world deployment measurement
- [Brandon Perry — Electric Charger Research (oss-security 2025)](https://seclists.org/oss-sec/2025/q3/10) — OCPP fuzzing, CitrineOS bugs
- [PlaxidityX — EVSE charge-port impersonation](https://plaxidityx.com/blog/blog-post/iso-15118-ev-cybersecurity-guide/)
- [Pen Test Partners — EV Charger Research](https://www.pentestpartners.com/security-blog/?s=charger)
- [OCPP Specification 2.0.1](https://www.openchargealliance.org/protocols/ocpp-201/)

## Tools

| Tool | Purpose | Link |
|---|---|---|
| HomePlugPWN | HomePlug AV/GreenPHY PLC attack tools | [FlUxIuS/HomePlugPWN](https://github.com/FlUxIuS/HomePlugPWN) |
| V2Gdecoder | Decode V2G/EXI messages | [FlUxIuS/V2Gdecoder](https://github.com/FlUxIuS/V2Gdecoder) |
| dsV2Gshark | Wireshark plugin for ISO 15118 V2G | [dspace-group/dsV2Gshark](https://github.com/dspace-group/dsV2Gshark) |
| pyPLC | Open-source CCS/ISO 15118 PLC stack | [uhi22/pyPLC](https://github.com/uhi22/pyPLC) |
| EVerest | Full EVSE framework (ISO 15118, OCPP, IEC 61851) | [EVerest/everest](https://github.com/EVerest/everest) |
| EVerest demo | Docker demo with simulated EV ↔ EVSE | [EVerest/everest-demo](https://github.com/EVerest/everest-demo) |
| RISE V2G | ISO 15118 reference implementation | [EVerest/ext-RISE-V2G](https://github.com/EVerest/ext-RISE-V2G) |
| SteVe | Open-source OCPP central system | [steve-community/steve](https://github.com/steve-community/steve) |
| OpenOCPP | Embedded OCPP 1.6J / 2.0.1 stack | [chargelab/openocpp](https://github.com/chargelab/openocpp) |
| python-ocpp | OCPP in Python | [mobilityhouse/ocpp](https://github.com/mobilityhouse/ocpp) |

## Lab

1. Run the [EVerest Docker demo](https://github.com/EVerest/everest-demo) for a full ISO 15118 + OCPP environment
2. Connect a simulated charger to [SteVe](https://github.com/steve-community/steve) via python-ocpp and inspect WebSocket traffic
3. Install [dsV2Gshark](https://github.com/dspace-group/dsV2Gshark) in Wireshark and explore sample V2G captures
4. Read the V2G Injector paper for PLC-layer attack methodology
