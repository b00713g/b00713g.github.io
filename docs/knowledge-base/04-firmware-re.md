# 4. Firmware Reversing

Going from "I have a binary blob" to "I found a command injection in the OTA handler."

## Try it first

### Extract a firmware image with binwalk

```bash
# Download any router/IoT firmware update from a vendor's support page
# (Netgear, TP-Link, D-Link all host firmware ZIPs publicly)

wget https://example-vendor.com/firmware-v2.1.bin

# Scan it
binwalk firmware-v2.1.bin

# Extract everything
binwalk -e firmware-v2.1.bin
cd _firmware-v2.1.bin.extracted/squashfs-root/

# Hunt for low-hanging fruit
grep -r "password" etc/
grep -r "api_key\|secret\|token" etc/ usr/
cat etc/shadow               # password hashes
find . -name "*.pem" -o -name "*.key"  # TLS private keys
strings usr/bin/httpd | grep -i "admin\|root\|debug"
```

**What you're looking for:** hardcoded credentials, debug endpoints left in production, private keys shipped in firmware, default password hashes you can crack.

### Load a bare-metal binary in Ghidra

When firmware doesn't have an ELF header (common for ECUs), you need to tell Ghidra what it is:

```
1. File → Import File → select your .bin dump
2. Language: ARM:LE:32:Cortex  (or TriCore, PowerPC:BE:32:e200, etc.)
3. Options → Base Address: 0x08000000  (STM32 flash base)
   For TriCore: 0xA0000000  (PFlash base)
   For PPC MPC5xxx: 0x00000000

4. After import: Analysis → Auto Analyze (accept defaults)
5. Go to the entry point (usually the reset vector at base+0x04)
6. Press 'D' to disassemble, 'F' to create a function
7. Window → Defined Strings — find every readable string
8. Cross-reference from strings back to the functions that use them
```

**What you're looking for:** string cross-refs lead you to UDS handlers, diagnostic routines, calibration access, and authentication checks. "Access Denied" → find the function → find the check → find the bypass.

---

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
