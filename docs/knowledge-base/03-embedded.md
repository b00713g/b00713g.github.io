# 3. Embedded Systems

An ECU is an embedded system with a CAN transceiver. To find real vulnerabilities you need to think at the register and instruction level.

## Read first

- [Azeria Labs — ARM Assembly Basics](https://azeria-labs.com/writing-arm-assembly-part-1/) — the best free ARM intro, do all 7 parts
- [Azeria Labs — ARM Exploit Development](https://azeria-labs.com/writing-arm-shellcode/)
- [VoidStar Security Roadmap](https://voidstarsec.com/roadmap/) — the full embedded hardware hacking progression
- [Wrong Baud's Blog](https://wrongbaud.github.io/) — real hardware teardowns with detailed methodology
- [Nightmare — Binary Exploitation](https://guyinatuxedo.github.io/) — x86 focused but primitives transfer

## OpenSecurityTraining2 (Xeno Kovah)

Free, deep-dive courses — some of the best training materials that exist. Relevant for automotive embedded work:

| Course | Hours | Link |
|---|---|---|
| Arch1001: x86-64 Assembly | ~40h | [ost2.fyi](https://p.ost2.fyi/courses/course-v1:OpenSecurityTraining2+Arch1001_x86-64_Asm+2021_v1/about) |
| Arch2001: x86-64 OS Internals | ~30h | [ost2.fyi](https://p.ost2.fyi/courses/course-v1:OpenSecurityTraining2+Arch2001_x86-64_OS_Internals+2024_v1/about) |
| Arch1005: RISC-V Assembly | ~10h | [ost2.fyi](https://p.ost2.fyi/courses/course-v1:OpenSecurityTraining2+Arch1005_IntroRISCV+2024_v1/about) |
| Arch1901: Zero to QEMU (emulator internals) | ~6h | [ost2.fyi](https://p.ost2.fyi/courses/course-v1:OpenSecurityTraining2+Arch1901_Zero2QEMU+2024_v1/about) |
| Arch4001: x86-64 Firmware Attack & Defense | ~20h | [ost2.fyi](https://p.ost2.fyi/courses/course-v1:OpenSecurityTraining2+Arch4001_x86-64_RVF+2021_v1/about) |
| Arch4031: Introductory coreboot | varies | [ost2.fyi](https://p.ost2.fyi/) |
| Intro to ARM (OST1 legacy) | ~8h | [opensecuritytraining.info](https://opensecuritytraining.info/IntroARM.html) |

[Full course catalog](https://p.ost2.fyi/courses) · [Learning path PDFs (visual skill trees)](https://ost2.fyi/)

## Automotive MCU Architectures: TriCore & PowerPC

Most automotive ECUs don't run ARM. The dominant architectures are **Infineon TriCore** (European OEMs — Bosch, Continental, Siemens) and **PowerPC e200** (GM, Ford, Stellantis, heavy trucks — NXP MPC55xx/57xx).

### TriCore (Infineon AURIX)

Found in: Bosch EDC17/MED17/MG1/MD1, Continental Simos, Valeo VD56 — practically every modern European powertrain ECU.

- [Wrong Baud — TriCore Basics: Hightec Toolchain in Linux + Ghidra](https://wrongbaud.github.io/posts/hightec-tricore-linux-ghidra/) — the practical starting point
- [icanhack.nl — Ghidra Tutorial (TriCore section)](https://icanhack.nl/knowledge-base/reverse-engineering/ghidra/) — memory map setup, processor selection, Small Data Area config
- [TriCore Architecture for ECU Reverse Engineers (Tuners Guild)](https://tunersguild.com/blog/tricore-reverse-engineering/) — registers, memory layout, Ghidra walkthrough
- [ReverseEngineer.net — Infineon TriCore Architecture](https://reverseengineer.net/infineon-tricore-ecu-reverse-engineering/) — TC1797 through AURIX TC3xx
- [Infineon — Introduction to TriCore in AURIX (official training video)](https://training.infineon.com/video/84444c3e-702b-406d-a621-503887020488)
- [AURIX Development Studio](https://www.infineon.com/design-resources/platforms/aurix-software-tools/aurix-tools/compiler) — free IDE/compiler
- Ghidra: `tricore:LE:32:default` (TC1.6.x) or `tc29x` for AURIX TC2xx. Load PFlash at `0xA0000000`.

### PowerPC e200 (NXP/Freescale MPC55xx/57xx)

Found in: GM, Ford, Stellantis petrol ECUs, Scania, MAN, Cummins truck ECUs, Delphi DCM6.x, Denso truck platforms.

- [icanhack.nl — Ghidra Tutorial (PowerPC VLE section)](https://icanhack.nl/knowledge-base/reverse-engineering/ghidra/) — loading offset, VLE vs Book-E, memory map
- Ghidra: `PowerPC:BE:32:e200` — the default variant won't decode VLE (Variable Length Encoding), which is what automotive PPC uses
- [Power ISA Specification](https://openpowerfoundation.org/specifications/isa/) — the architecture reference
- [EREF: A Programmer's Reference Manual for Freescale Power Architecture](https://www.nxp.com/docs/en/reference-manual/EREF_RM.pdf) — e200/e500 core reference

### Supporting fundamentals

- [Demystifying Bitwise Operations](https://www.andreinc.net/2023/02/01/demystifying-bitwise-ops) — register manipulation and protocol parsing
- [Makefile Tutorial by Example](https://makefiletutorial.com/) — most embedded projects use Make
- [Programming FTDI Devices in Python](https://iosoft.blog/2018/12/02/ftdi-python-part-1/) — FTDI chips are in every debug adapter
- [Encryption for Embedded Linux (Sergio Prado)](https://sergioprado.blog/introduction-to-encryption-for-embedded-linux-developers/) — 3-part series
- [RISC-V Bytes: Zephyr on ESP32](https://danielmangum.com/posts/risc-v-bytes-zephyr-on-esp32/) — RTOS on real hardware

## Suggested order

1. [exploit.education Phoenix](https://exploit.education/phoenix/) — exploit primitives (x86)
2. [Azeria Labs ARM exercises](https://azeria-labs.com/writing-arm-assembly-part-1/) — shift to ARM
3. [Microcorruption](https://microcorruption.com/) — embedded exploitation (MSP430)
4. [VoidStar UART/SPI/JTAG labs](https://voidstarsec.com/roadmap/) — real hardware
5. Pick your automotive architecture: TriCore (European ECUs) or PowerPC (American/truck ECUs) using the resources above

## Tools

| Tool | Purpose | Link |
|---|---|---|
| GDB + GEF | Debugger with exploit-dev extensions | [hugsy/gef](https://github.com/hugsy/gef) |
| QEMU | ARM/MIPS emulation | [qemu.org](https://www.qemu.org/) |
| Ghidra | RE framework (TriCore, PPC VLE, RH850 support built-in) | [ghidra-sre.org](https://ghidra-sre.org/) |
| pwntools | Exploit scripting | [Gallopsled/pwntools](https://github.com/Gallopsled/pwntools) |
| OpenOCD | JTAG/SWD interface | [openocd.org](https://openocd.org/) |
| AURIX Development Studio | Infineon's free TriCore IDE/compiler | [infineon.com](https://www.infineon.com/design-resources/platforms/aurix-software-tools/aurix-tools/compiler) |
