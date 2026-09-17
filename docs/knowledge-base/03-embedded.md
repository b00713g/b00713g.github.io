# 3. Embedded Systems

An ECU is an embedded system with a CAN transceiver. To find real vulnerabilities you need to think at the register and instruction level.

## Try it first

These are the three most common ways into an embedded target. Once you've done each once, you'll recognize the patterns on any device.

### UART — Get a root shell

UART is the serial console most embedded devices expose on the PCB. If you find TX/RX pins and the shell isn't locked down, you're root.

```bash
# Step 1: Find the UART pins on the PCB
# Look for 4 unpopulated pads in a row: VCC, TX, RX, GND
# Use a multimeter:
#   GND = 0V (continuity to ground plane)
#   VCC = 3.3V or 5V (steady)
#   TX  = fluctuating voltage (data being sent)
#   RX  = steady high or floating (waiting for input)

# Step 2: Connect your USB-UART adapter (Tigard, CP2102, or FTDI)
#   Adapter TX  →  Device RX
#   Adapter RX  →  Device TX
#   Adapter GND →  Device GND
#   (do NOT connect VCC unless you know what you're doing)

# Step 3: Find the baud rate and connect
# Common automotive baud rates: 115200, 57600, 38400, 9600

# Try the most common first:
screen /dev/ttyUSB0 115200

# Or use baudrate.py to auto-detect:
# pip install baudrate
# baudrate -p /dev/ttyUSB0
```

```
# What you'll see if it works:
U-Boot 2019.07 (Oct 14 2020)
...
Hit any key to stop autoboot: 0
=>                              # <-- bootloader shell!

# Or after boot:
login: root
Password:                       # <-- blank password? you're in
root@device:~#
```

**What you're looking for:** bootloader shell access (can dump flash, change boot args), root login without password, kernel boot logs leaking partition layout and debug info.

### SPI Flash — Dump the firmware

Most embedded devices store firmware on a SPI NOR flash chip (W25Q, MX25L, AT25SF — usually an 8-pin SOIC near the main processor). You can read the entire contents without even powering the device.

```bash
# Step 1: Identify the flash chip
# Read the markings on the 8-pin chip (e.g., W25Q128, MX25L6406E)

# Step 2: Connect with a clip or solder wires
# SOIC-8 test clip (~$5) snaps right onto the chip
# Connect to Tigard, Bus Pirate, or a CH341A programmer (~$5)
#
# CH341A pinout to SPI flash:
#   CS   →  pin 1
#   MISO →  pin 2
#   MOSI →  pin 5
#   CLK  →  pin 6
#   GND  →  pin 4
#   VCC  →  pin 8

# Step 3: Read the flash with flashrom
sudo apt install flashrom
flashrom -p ch341a_spi -r firmware_dump.bin

# Verify — read twice and compare (noisy connections = bad dumps)
flashrom -p ch341a_spi -r firmware_dump2.bin
md5sum firmware_dump.bin firmware_dump2.bin
# If hashes match, your dump is clean

# Step 4: Extract the filesystem
binwalk -e firmware_dump.bin
ls _firmware_dump.bin.extracted/
# You'll typically find: squashfs-root/, kernel, bootloader
```

```
# Example binwalk output:
DECIMAL       HEXADECIMAL     DESCRIPTION
0             0x0             uBoot header, image name: "U-Boot 2019"
65536         0x10000         uBoot environment
262144        0x40000         uImage header, Linux kernel
2097152       0x200000        Squashfs filesystem, little endian
```

**What you're looking for:** extracted filesystem with `/etc/shadow` (password hashes), hardcoded keys in config files, web server source code, debug binaries left on the image.

### JTAG/SWD — Debug a running processor

JTAG and SWD are the interfaces chip manufacturers use for debugging. If the debug port isn't disabled, you can halt the CPU, read all memory, and single-step through code.

```bash
# Step 1: Identify JTAG/SWD pins on the PCB
# Look for: 10-pin or 20-pin headers, or unpopulated pads
# labeled TCK, TMS, TDI, TDO (JTAG) or SWCLK, SWDIO (SWD)
# Use JTAGulator or manual probing to identify pins

# Step 2: Connect your debug probe
# J-Link EDU, Tigard, or ST-Link V2 (~$5 clone)
# For SWD (ARM Cortex-M — most common in automotive):
#   Probe SWCLK → Target SWCLK
#   Probe SWDIO → Target SWDIO
#   Probe GND   → Target GND

# Step 3: Connect with OpenOCD
cat > openocd.cfg << 'EOF'
source [find interface/jlink.cfg]
transport select swd
source [find target/stm32f4x.cfg]
EOF

openocd -f openocd.cfg
# In another terminal:
gdb-multiarch
(gdb) target remote :3333
(gdb) monitor halt
(gdb) monitor flash banks     # list flash regions
(gdb) dump binary memory firmware.bin 0x08000000 0x08100000

# Or with J-Link Commander directly:
JLinkExe -device STM32F407VG -if SWD -speed 4000
J-Link> connect
J-Link> halt
J-Link> savebin dump.bin 0x08000000 0x100000
```

**What you're looking for:** full firmware dump even when SPI isn't accessible, ability to set breakpoints on crypto functions or auth checks, read-out protection (RDP) level — if it's Level 0, you just dumped everything.

---

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

- [Matt Brown (Brown Fine Security) — YouTube](https://www.youtube.com/@mattbrwn) — 200K+ subscribers, largest active hardware hacking channel. Teardowns, UART exploitation, firmware extraction, logic analysis walkthroughs
- [All About UART (Brown Fine Security Training)](https://training.brownfinesecurity.com/l/pdp/all-about-uart) — **free course** covering UART from voltage level to exploitation, includes multimeter, logic analyzer, and soldering basics
- [A Beginner's Guide to Hardware Hacking Tools (Matt Brown)](https://brownfinesecurity.com/blog/hardware-hacking-tools-beginners-guide) — prioritized buying guide for getting started
- [IoT Pentesting Basics: Root Shell via UART Exploitation (Matt Brown)](https://brownfinesecurity.com/blog/iot-pentesting-basics-uart-root-shells) — UART enumeration and exploitation walkthrough
- [Digital Signal Analysis for Hardware Hackers (paid)](https://training.brownfinesecurity.com/l/pdp/digital-signal-analysis-for-hardware-hackers) — hands-on UART/SPI/I2C signal capture with logic analyzers on real hardware
- [Beginner's Guide to IoT and Hardware Hacking (paid)](https://training.brownfinesecurity.com/l/pdp/beginner-s-guide-to-iot-and-hardware-hacking) — bridging software pentesting skills to hardware/IoT
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
