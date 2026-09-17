# 4. Firmware Reversing

Going from "I have a binary blob" to "I found a command injection in the OTA handler."

## Read first

- [icanhack.nl — ECU Flashing](https://icanhack.nl/knowledge-base/reverse-engineering/ecu-flashing/)
- [icanhack.nl — OEM Update Files](https://icanhack.nl/knowledge-base/reverse-engineering/oem-update-files/)
- [icanhack.nl — Ghidra Tutorial](https://icanhack.nl/knowledge-base/reverse-engineering/ghidra/)
- [icanhack.nl blog](https://icanhack.nl/blog/) — Willem Melching's automotive firmware RE walkthroughs, including:
    - [Hacking a VW Golf Power Steering ECU — Part 1](https://icanhack.nl/blog/vw-part1/)
- [Wrong Baud's Blog](https://wrongbaud.github.io/) — firmware extraction and analysis on real targets
- [Wrong Baud — Ghidra Training (Hackaday U)](https://wrongbaud.github.io/posts/ghidra-training/) — four-session video course
- [Wrong Baud — Writing a Ghidra Loader: STM32 Edition](https://wrongbaud.github.io/posts/writing-a-ghidra-loader/)

### IoT Firmware RE Walkthroughs (Matt Brown / Brown Fine Security)

Real-world IoT firmware teardowns — the methodology transfers directly to automotive embedded targets:

- [Firmware Extraction and Analysis of Uniview Camera](https://brownfinesecurity.com/blog/firmware-extraction-and-analysis-of-uniview-camera) — searching firmware for hardcoded secrets
- [Bypassing Restricted Shell on Uniview Security Camera](https://brownfinesecurity.com/blog/bypassing-restricted-shell-on-uniview-security-camera) — unlocked bootloader to root shell
- [Reverse Engineering Hanwha Camera Firmware Decryption with IDA Pro](https://brownfinesecurity.com/blog/hanwha-firmware-file-decryption) — encrypted firmware file analysis
- [Uncovering Hardcoded Root Password in VStarcam CB73](https://brownfinesecurity.com/blog/vstarcam-cb73-hardcoded-root-password) — firmware extraction and credential recovery
- [Proprietary Encryption Protocol Analysis in VStarcam CB73](https://brownfinesecurity.com/blog/vstarcam-cb73-proprietary-encryption-analysis) — crypto flaws in UDP P2P protocol
- [An IoT Pentesting Roadmap (Matt Brown)](https://brownfinesecurity.com/blog/iot-pentesting-roadmap) — structured methodology for full IoT assessments

## Suggested order

1. **binwalk** on practice firmware — download router firmware, extract filesystems, find binaries
2. **Ghidra on a known target** — [DVRF](https://github.com/praetorian-inc/DVRF), [DVAR](https://blog.exploitlab.net/2018/01/dvar-damn-vulnerable-arm-router.html), or follow along with [NCC Group's NETGEAR router analysis](https://research.nccgroup.com/2023/05/15/netgear-routers-a-playground-for-hackers/)
3. **Bare-metal blob** — load a raw binary in Ghidra, figure out base address and architecture manually
4. **Emulation** — use [Unicorn Engine](https://github.com/unicorn-engine/unicorn) to emulate specific functions from firmware

## Tools

| Tool | Purpose | Link |
|---|---|---|
| binwalk | Firmware scanning and extraction | [ReFirmLabs/binwalk](https://github.com/ReFirmLabs/binwalk) |
| Ghidra | RE framework with ARM/TriCore support | [ghidra-sre.org](https://ghidra-sre.org/) |
| Unicorn Engine | CPU emulator for targeted function analysis | [unicorn-engine/unicorn](https://github.com/unicorn-engine/unicorn) |
| firmwalker | Searches extracted firmware for interesting files | [craigz28/firmwalker](https://github.com/craigz28/firmwalker) |
| FACT | Automated firmware analysis platform | [fkie-cad/FACT_core](https://github.com/fkie-cad/FACT_core) |
| rizin | CLI-first RE framework | [rizinorg/rizin](https://github.com/rizinorg/rizin) |
