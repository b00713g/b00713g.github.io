# Learning Roadmap

A structured path from zero to automotive security research.

Where a definitive external resource exists, we link to it instead of rewriting it. The [icanhack.nl Knowledge Base](https://icanhack.nl/knowledge-base/networks/introduction/) covers vehicle networks and diagnostics. The [VoidStar Security Roadmap](https://voidstarsec.com/roadmap/) covers embedded hardware hacking. This roadmap connects them with automotive-specific context and fills the gaps.

---

## Prerequisites

Before starting, you need:

- Linux command line comfort → [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
- Basic networking (IP, ports, Wireshark) → [Hack The Box Starting Point](https://app.hackthebox.com/starting-point)
- A scripting language (Python preferred) → [Automate the Boring Stuff](https://automatetheboringstuff.com/)

---

## Phase 1 — CAN Bus Fundamentals

*Get comfortable with the protocol that connects every ECU in a vehicle.*

??? example "What to learn"
    - CAN 2.0A/B frame structure, arbitration, bus access
    - CAN-FD differences
    - DBC files and signal decoding
    - **Read:** [icanhack.nl — Controller Area Network](https://icanhack.nl/knowledge-base/networks/controller-area-network/)
    - **Read:** [Car Hacker's Handbook Ch. 2](http://opengarages.org/handbook/) (free)
    - **Read:** [CSS Electronics — CAN Bus Intro](https://www.csselectronics.com/pages/can-bus-simple-intro-tutorial)

??? example "Lab: ICSim on Virtual CAN"
    No hardware needed.

    1. `sudo modprobe vcan && sudo ip link add dev vcan0 type vcan && sudo ip link set up vcan0`
    2. Run [ICSim](https://github.com/zombieCraig/ICSim): `./icsim vcan0` + `./controls vcan0`
    3. `candump vcan0` — identify which IDs control turn signals, doors, speedometer
    4. `cansend vcan0 <id>#<data>` — replay frames to trigger actions
    5. Write a Python script with [python-can](https://github.com/hardbyte/python-can) to toggle all doors

    **Done when:** You can map 3+ arbitration IDs to functions and inject frames reliably.

→ [Full chapter](knowledge-base/01-can.md)

---

## Phase 2 — Vehicle Networks & Diagnostics

*CAN is one bus. Modern vehicles have many, plus diagnostic protocols layered on top.*

??? example "What to learn"
    - LIN, FlexRay, Automotive Ethernet, SOME/IP
    - UDS (ISO 14229), DoIP (ISO 13400), ISO-TP, OBD-II
    - Gateway architecture and domain segmentation
    - **Read:** [icanhack.nl — Full Knowledge Base](https://icanhack.nl/knowledge-base/networks/introduction/) (Chapters 1–14)
    - **Read:** [Scapy Automotive Documentation](https://scapy.readthedocs.io/en/latest/layers/automotive.html)
    - **Watch:** [Nils Weiss — Automotive Network Scans with Scapy (Troopers 2022)](https://www.youtube.com/watch?v=lUfmlmFwC1A)

??? example "Lab: UDS scanning with Scapy"
    1. Set up a simulated UDS ECU using `scapy.contrib.automotive.ecu`
    2. Run the ISO-TP scanner: `isotpscanner -c vcan0 -s 0x600 -e 0x6ff`
    3. Use `UDS_Scanner` with `UDS_ServiceEnumerator` and `UDS_DSCEnumerator`
    4. Observe how available services change across diagnostic sessions and security access levels

    See also: [CaringCaribou](https://github.com/CaringCaribou/caringcaribou) for quicker UDS enumeration.

→ [Full chapter](knowledge-base/02-networks-diag.md)

---

## Phase 3 — Embedded Systems

*An ECU is an embedded system. Learn to think at the register level.*

??? example "What to learn"
    - ARM architecture (Cortex-M, Cortex-A), common automotive MCUs (TriCore, RH850)
    - Memory layout, stack/heap, memory-mapped I/O
    - Buffer overflows, format strings on embedded targets
    - Debug interfaces: JTAG, SWD, UART
    - **Do:** [exploit.education Phoenix](https://exploit.education/phoenix/) — Stack Zero through Six, Format Zero through Four
    - **Do:** [Azeria Labs ARM Assembly](https://azeria-labs.com/writing-arm-assembly-part-1/) — all 7 parts
    - **Do:** [Microcorruption](https://microcorruption.com/) — Tutorial through Addis Ababa
    - **Read:** [VoidStar Security Roadmap](https://voidstarsec.com/roadmap/) — the entire hardware hacking progression

??? example "Recommended order"
    1. **Phoenix** — learn exploit primitives in a friendly x86 environment
    2. **Azeria Labs** — shift to ARM (what most ECUs run)
    3. **Microcorruption** — embedded architecture (MSP430), browser-based debugger, no setup
    4. **VoidStar UART/SPI/JTAG path** — when you're ready for real hardware

→ [Full chapter](knowledge-base/03-embedded.md)

---

## Phase 4 — Firmware Reversing

*Pull apart the code running on ECUs, telematics units, and gateways.*

??? example "What to learn"
    - Firmware acquisition (UART dump, JTAG extraction, OTA packages)
    - Firmware structure (headers, filesystems, bare-metal blobs)
    - Static analysis with Ghidra, base address identification
    - Emulation with QEMU / Unicorn
    - **Read:** [icanhack.nl — ECU Flashing](https://icanhack.nl/knowledge-base/reverse-engineering/ecu-flashing/)
    - **Read:** [icanhack.nl — OEM Update Files](https://icanhack.nl/knowledge-base/reverse-engineering/oem-update-files/)
    - **Read:** [icanhack.nl — Ghidra Tutorial](https://icanhack.nl/knowledge-base/reverse-engineering/ghidra/)
    - **Read:** [Willem Melching (icanhack) blog posts](https://icanhack.nl/blog/) — real automotive firmware RE
    - **Read:** [Wrong Baud's Blog](https://wrongbaud.github.io/) — firmware extraction methodology
    - **Watch:** [Wrong Baud — Ghidra Training (Hackaday U)](https://wrongbaud.github.io/posts/ghidra-training/)

??? example "Lab progression"
    1. **binwalk + firmware images** — download router firmware from vendor sites, extract filesystems, find binaries
    2. **Ghidra on a known target** — [DVRF](https://github.com/praetorian-inc/DVRF) or [DVAR](https://blog.exploitlab.net/2018/01/dvar-damn-vulnerable-arm-router.html)
    3. **Bare-metal blob in Ghidra** — practice setting base address and architecture manually
    4. **Emulate a function** — use [Unicorn Engine](https://github.com/unicorn-engine/unicorn) to emulate a CAN message handler or UDS routine

→ [Full chapter](knowledge-base/04-firmware-re.md)

---

## Phase 5 — EVSE & EV Charging

*The charger is a networked embedded system with its own attack surface, connected to both the vehicle and the cloud.*

??? example "What to learn"
    - ISO 15118 / Plug & Charge, OCPP, IEC 61851
    - PLC (HomePlug GreenPHY) as the physical layer between EV and EVSE
    - OCPP backend security
    - **Read:** [icanhack.nl — EV Charging Research](https://icanhack.nl/knowledge-base/existing-research/ev-charging/)
    - **Read:** [V2G Injector (Dudek et al., SSTIC 2019)](https://www.sstic.org/2019/presentation/v2g_injector_playing_with_electric_cars_and_charging_stations_via_powerline/)
    - **Read:** [Brokenwire — CCS charging disruption](https://brokenwire.fail/)
    - **Read:** [Brandon Perry — Electric Charger Research (2025)](https://seclists.org/oss-sec/2025/q3/10)

??? example "Lab: OCPP and V2G analysis"
    1. Run [EVerest Docker demo](https://github.com/EVerest/everest-demo) — full ISO 15118 + OCPP stack
    2. Connect a simulated charger to [SteVe](https://github.com/steve-community/steve) via [python-ocpp](https://github.com/mobilityhouse/ocpp)
    3. Install [dsV2Gshark](https://github.com/dspace-group/dsV2Gshark) in Wireshark, open sample V2G PCAPs
    4. Decode the ISO 15118 handshake: SDP → session setup → authorization → charging

→ [Full chapter](knowledge-base/05-evse.md)

---

## Phase 6 — RF, Wireless & Adjacent Surfaces

*Key fobs, TPMS, BLE phone-as-a-key, cellular telematics, V2X.*

??? example "What to learn"
    - Key fob attacks (relay, RollJam, crypto weaknesses)
    - BLE/Bluetooth vehicle companion app security
    - TPMS sniffing
    - Cellular/telematics attack surface
    - **Read:** [icanhack.nl — Wireless and RF](https://icanhack.nl/knowledge-base/reverse-engineering/wireless-rf/)
    - **Read:** [icanhack.nl — Remote Keyless Entry](https://icanhack.nl/knowledge-base/existing-research/remote-keyless-entry/)
    - **Read:** [Samy Kamkar — RollJam and automotive RF](https://samy.pl/)
    - **Start with:** RTL-SDR ($25) + [URH](https://github.com/jopohl/urh) — receive-only, legal practice on your own devices

→ [Full chapter](knowledge-base/06-rf.md)

---

## Phase 7 — Android Automotive & Infotainment

*AAOS runs natively on head units with direct access to vehicle signals.*

??? example "What to learn"
    - AAOS vs. Android Auto distinction
    - Vehicle HAL (VHAL) as the IVI-to-vehicle security boundary
    - Third-party app permission model
    - **Read:** [icanhack.nl — Infotainment & Telematics](https://icanhack.nl/knowledge-base/existing-research/infotainment-telematics/)
    - **Read:** [Security Analysis of Android Automotive (Pese et al., 2020)](https://www.researchgate.net/publication/340632296)
    - **Lab:** Set up the [AAOS emulator](https://developer.android.com/training/cars/testing) in Android Studio, explore VHAL with `adb shell dumpsys car_service`

→ [Full chapter](knowledge-base/07-android-auto.md)

---

## Phase 8 — Hardware Security

*Side-channel analysis, fault injection, secure boot bypass.*

??? example "What to learn"
    - Power analysis (SPA/DPA/CPA), electromagnetic emissions
    - Voltage glitching, clock glitching, EMFI
    - Readout protection bypass, secure boot attacks
    - **Read:** [icanhack.nl — Fault Injection](https://icanhack.nl/knowledge-base/existing-research/fault-injection/)
    - **Read:** [BAM BAM!! — EMFI on Automotive ECUs (O'Flynn)](https://eprint.iacr.org/2020/937.pdf)
    - **Do:** [ChipWhisperer Jupyter Labs](https://chipwhisperer.readthedocs.io/en/latest/getting-started.html) — power analysis → AES CPA → voltage glitching
    - **Do:** [RHme CTF challenges](https://github.com/Riscure/Rhme-2016)
    - **Reference:** [VoidStar — Fault Injection resources](https://voidstarsec.com/blog/replicant-part-1)

→ [Full chapter](knowledge-base/08-hardware-security.md)

---

## Top 10 Talks — Foundational to Modern

The talks that defined the field and the recent ones pushing it forward. Watch these in order for a compressed history of automotive security research.

### The Foundations (2013–2017)

| # | Talk | Speaker(s) | Year | Why it matters |
|---|---|---|---|---|
| 1 | Adventures in Automotive Networks and Control Units | Charlie Miller, Chris Valasek | 2013 | The talk that started it. CAN injection via OBD-II on a Ford and Toyota. DEF CON 21. |
| 2 | Remote Exploitation of an Unaltered Passenger Vehicle | Charlie Miller, Chris Valasek | 2015 | Jeep Cherokee remote exploit → 1.4M vehicle recall. The moment the industry had to take this seriously. Black Hat 2015. |
| 3 | [Drive It Like You Hacked It / RollJam](https://samy.pl/) | Samy Kamkar | 2015 | Demonstrated real-world key fob rolling code bypass with a $30 device. DEF CON 23. |
| 4 | Free-Fall: Hacking Tesla from Wireless to CAN Bus | Keen Security Lab | 2017 | Multi-stage remote chain: Wi-Fi → browser → kernel → CAN bus on a Tesla Model S. Black Hat 2017. |
| 5 | [Car Hacking 101](http://opengarages.org/handbook/) | Craig Smith | 2014–ongoing | Not one talk but the Car Hacker's Handbook + Open Garages community that trained the first generation of researchers. |

### The Modern Era (2019–2025)

| # | Talk | Speaker(s) | Year | Why it matters |
|---|---|---|---|---|
| 6 | [Automotive Penetration Testing with Scapy](https://troopers.de/troopers19/agenda/znxvht/) | Nils Weiss, Enrico Pozzobon | 2019 | Introduced the Scapy automotive layer. Changed how the community tools diagnostic protocol testing. Troopers 2019. |
| 7 | [V2G Injector: Through the Power-Line](https://www.sstic.org/2019/presentation/v2g_injector_playing_with_electric_cars_and_charging_stations_via_powerline/) | Sébastien Dudek et al. | 2019 | Opened EV charging as a research field. PLC-layer MITM on ISO 15118. SSTIC 2019. |
| 8 | [BAM BAM!! EMFI for in-situ Automotive ECU Attacks](https://eprint.iacr.org/2020/937.pdf) | Colin O'Flynn | 2020 | Proved EMFI secure boot bypass works on real automotive ECUs in real vehicles, not just lab targets. escar 2020. |
| 9 | [Automotive Network Scans with Scapy](https://www.youtube.com/watch?v=lUfmlmFwC1A) | Nils Weiss | 2022 | Stateful UDS/DoIP/HSFZ scanning across ECU state machines. The current standard for automated automotive protocol testing. Troopers 2022. |
| 10 | [Glitching in 3D: Low Cost EMFI Attacks](https://voidstarsec.com/csw-2024) | Matthew Alt (Wrong Baud) | 2024 | PicoEMP + 3D printer for precise EMFI probe positioning. Democratized hardware fault injection. CanSecWest 2024. |

### Honorable mentions

- [Hacking a VW Golf Power Steering ECU](https://icanhack.nl/blog/vw-part1/) — Willem Melching (icanhack.nl) — real VW ECU teardown
- [Ghidra Training Course](https://wrongbaud.github.io/posts/ghidra-training/) — Wrong Baud / Hackaday U — four-session free course that taught thousands
- [Replicant: Reproducing a Fault Injection Attack on Trezor One](https://voidstarsec.com/blog/replicant-part-1) — Wrong Baud — voltage glitching methodology
