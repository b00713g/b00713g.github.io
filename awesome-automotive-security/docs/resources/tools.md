# Tools

See also: [hexsecs/awesome-automotive-security](https://github.com/hexsecs/awesome-automotive-security) · [awesome-vehicle-security](https://github.com/jaredthecoder/awesome-vehicle-security) · [awesome-canbus](https://github.com/iDoka/awesome-canbus)

## CAN & Vehicle Networks

| Tool | Purpose | Link |
|---|---|---|
| can-utils | candump, cansend, cangen, cansniffer | [linux-can/can-utils](https://github.com/linux-can/can-utils) |
| ICSim | Instrument cluster simulator | [zombieCraig/ICSim](https://github.com/zombieCraig/ICSim) |
| SavvyCAN | GUI CAN analyzer + DBC + fuzzing | [collin80/SavvyCAN](https://github.com/collin80/SavvyCAN) |
| python-can | Python CAN library | [hardbyte/python-can](https://github.com/hardbyte/python-can) |
| cantools | DBC parsing and signal decoding | [cantools/cantools](https://github.com/cantools/cantools) |
| CANToolz (YACHT) | CAN black-box analysis framework | [CANToolz/CANToolz](https://github.com/CANToolz/CANToolz) |
| Lindwurm | CAN bus tracing and fuzzing (Burp-style) | [lindwurm-can/lindwurm](https://github.com/lindwurm-can/lindwurm) |
| PiCCANTE | Raspberry Pi Pico CAN bus tool | [Alia5/PiCCANTE](https://github.com/Alia5/PiCCANTE) |

## Diagnostics (UDS / DoIP / OBD)

| Tool | Purpose | Link |
|---|---|---|
| scapy (automotive) | UDS, DoIP, HSFZ, ISO-TP, SOME/IP, GMLAN, XCP + scanners | [secdev/scapy](https://github.com/secdev/scapy) |
| python-udsoncan | UDS implementation | [pylessard/python-udsoncan](https://github.com/pylessard/python-udsoncan) |
| CaringCaribou | Automotive security testing (nmap of CAN) | [CaringCaribou/caringcaribou](https://github.com/CaringCaribou/caringcaribou) |
| gallia | Automotive pentesting (Fraunhofer) | [Fraunhofer-AISEC/gallia](https://github.com/Fraunhofer-AISEC/gallia) |
| UDSim | UDS ECU simulator and fuzzer | [zombieCraig/UDSim](https://github.com/zombieCraig/UDSim) |
| UnlockECU | Seed-key unlocking for Bosch/Continental/Delphi | [jglim/UnlockECU](https://github.com/jglim/UnlockECU) |
| pq-flasher | VW PQ35 EPS reflashing tools | [I-CAN-hack/pq-flasher](https://github.com/I-CAN-hack/pq-flasher) |
| SecOC Key Extractor | Extract SecOC keys from Toyota vehicles | [i-can-hack/secoc](https://github.com/i-can-hack/secoc) |

## Automotive Ethernet

| Tool | Purpose | Link |
|---|---|---|
| vsomeip | SOME/IP open-source stack | [COVESA/vsomeip](https://github.com/COVESA/vsomeip) |
| ICS CAP | Wireshark plugin for Automotive Ethernet | [intrepidcs.com](https://intrepidcs.com/products/software/ics-cap/) |
| eth-ws-someip | SOME/IP Wireshark LUA dissectors | [jamores/eth-ws-someip](https://github.com/jamores/eth-ws-someip) |

## EVSE / V2G

| Tool | Purpose | Link |
|---|---|---|
| HomePlugPWN | HomePlug AV/GreenPHY PLC tools | [FlUxIuS/HomePlugPWN](https://github.com/FlUxIuS/HomePlugPWN) |
| V2Gdecoder | V2G/EXI message decoder | [FlUxIuS/V2Gdecoder](https://github.com/FlUxIuS/V2Gdecoder) |
| dsV2Gshark | Wireshark ISO 15118 plugin | [dspace-group/dsV2Gshark](https://github.com/dspace-group/dsV2Gshark) |
| pyPLC | CCS/ISO 15118 PLC stack | [uhi22/pyPLC](https://github.com/uhi22/pyPLC) |
| EVerest | Full EVSE framework (ISO 15118, OCPP) | [EVerest/everest](https://github.com/EVerest/everest) |
| SteVe | OCPP central system | [steve-community/steve](https://github.com/steve-community/steve) |
| OpenOCPP | Embedded OCPP stack | [chargelab/openocpp](https://github.com/chargelab/openocpp) |
| python-ocpp | OCPP in Python | [mobilityhouse/ocpp](https://github.com/mobilityhouse/ocpp) |
| tesla-opener | Open Tesla charge port via HackRF | [rgerganov/tesla-opener](https://github.com/rgerganov/tesla-opener) |

## RF & Wireless

| Tool | Purpose | Link |
|---|---|---|
| Sniffle (NCC Group) | BLE 5 + 4.x sniffer — the gold standard | [nccgroup/Sniffle](https://github.com/nccgroup/Sniffle) |
| WHAD | Wireless HAck Devices framework (BLE, Zigbee, ESB, Unifying, PHY) | [whad-team/whad-client](https://github.com/whad-team/whad-client) |
| Flipper Zero | Sub-GHz, RFID, IR, GPIO multi-tool | [flipperzero.one](https://flipperzero.one/) |
| Flipper-ARF | Automotive Flipper firmware (Keeloq, rolling codes, VAG) | [D4C1-Labs/Flipper-ARF](https://github.com/D4C1-Labs/Flipper-ARF) |
| Proxmark3 | RFID/NFC testing (immobilizers, key cards) | [proxmark.com](https://proxmark.com/) |
| URH (Universal Radio Hacker) | SDR record → analyze → decode → generate | [jopohl/urh](https://github.com/jopohl/urh) |
| GNU Radio | SDR signal processing framework | [gnuradio.org](https://gnuradio.org/) |
| rtl_433 | ISM band decoder for TPMS/key fobs (315/433/868/915 MHz) | [merbanan/rtl_433](https://github.com/merbanan/rtl_433) |
| KAT (KeyFob Analysis Toolkit) | Key fob signal analysis/decode/retransmit | [KaraZajac/KAT](https://github.com/KaraZajac/KAT) |

## Firmware & RE

| Tool | Purpose | Link |
|---|---|---|
| Ghidra | RE framework (TriCore, PPC VLE, RH850 built-in) | [ghidra-sre.org](https://ghidra-sre.org/) |
| binwalk | Firmware extraction | [ReFirmLabs/binwalk](https://github.com/ReFirmLabs/binwalk) |
| Unicorn Engine | CPU emulator | [unicorn-engine/unicorn](https://github.com/unicorn-engine/unicorn) |
| FACT | Automated firmware analysis | [fkie-cad/FACT_core](https://github.com/fkie-cad/FACT_core) |
| rizin | CLI RE framework | [rizinorg/rizin](https://github.com/rizinorg/rizin) |

## Hardware Security

| Tool | Purpose | Link |
|---|---|---|
| ChipWhisperer | SCA + fault injection platform | [newaetech/chipwhisperer](https://github.com/newaetech/chipwhisperer) |
| PicoGlitcher | Low-cost RP2040 voltage glitcher | [MKesenheimer/PicoGlitcher](https://github.com/MKesenheimer/PicoGlitcher) |
| findus | Fault injection automation | [MKesenheimer/findus](https://github.com/MKesenheimer/findus) |
| HardwareAllTheThings | Hardware hacking reference | [swisskyrepo/HardwareAllTheThings](https://github.com/swisskyrepo/HardwareAllTheThings) |

## Hardware & Debug Adapters

| Tool | Purpose | Link |
|---|---|---|
| Tigard | Multi-protocol FTDI debug adapter (UART, SPI, I2C, JTAG, SWD) | [tigard-tools/tigard](https://github.com/tigard-tools/tigard) |
| Saleae Logic | Logic analyzer (protocol decode, SPI/I2C/UART/CAN capture) | [saleae.com](https://www.saleae.com/) |
| Bus Pirate | Universal serial interface tool | [buspirate.com](https://buspirate.com/) |
| J-Link EDU | JTAG/SWD debug probe | [segger.com](https://www.segger.com/products/debug-probes/j-link/models/j-link-edu/) |
| OpenOCD | JTAG/SWD interface software | [openocd.org](https://openocd.org/) |
| GDB + GEF | Debugger with exploit-dev extensions | [hugsy/gef](https://github.com/hugsy/gef) |
| QEMU | Architecture emulation | [qemu.org](https://www.qemu.org/) |
| pwntools | Exploit scripting | [Gallopsled/pwntools](https://github.com/Gallopsled/pwntools) |
| AURIX Development Studio | Infineon's free TriCore IDE/compiler | [infineon.com](https://www.infineon.com/design-resources/platforms/aurix-software-tools/aurix-tools/compiler) |

## Diagramming

| Tool | Purpose | Link |
|---|---|---|
| Excalidraw | Whiteboard for attack flows | [excalidraw.com](https://excalidraw.com/) |
