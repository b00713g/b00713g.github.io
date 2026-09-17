# 0. Lab Setup

Everything you need to start — copy-paste into a terminal. No hardware required for Phase 1.

---

## Pick Your Base OS

Any Debian-based Linux works. If you're starting from scratch:

```
# Option A: Ubuntu 22.04+ (clean, minimal, recommended)
# Download: https://ubuntu.com/download/desktop

# Option B: Kali Linux (pre-loaded with security tools)
# Download: https://www.kali.org/get-kali/

# Option C: Run either in a VM (VirtualBox/VMware)
# Allocate: 4+ CPU cores, 8GB+ RAM, 60GB+ disk
```

If you're on macOS or Windows and don't want to dual-boot, a VM is fine for everything except USB passthrough to hardware adapters (which needs extra config).

---

## Phase 1 — CAN Bus Tools

The minimum to start sniffing and injecting CAN frames today.

```bash
# ──────────────────────────────────────────────
#  CAN bus essentials
# ──────────────────────────────────────────────

sudo apt update && sudo apt install -y \
    can-utils \
    libsdl2-dev \
    libsdl2-image-dev \
    git \
    build-essential \
    python3-pip \
    python3-venv

# Create a Python venv for all automotive tooling
python3 -m venv ~/auto-lab
source ~/auto-lab/bin/activate

# Python CAN libraries
pip install python-can cantools scapy

# ICSim — instrument cluster simulator (your first lab)
cd ~ && git clone https://github.com/zombieCraig/ICSim.git
cd ICSim && make
```

### Verify it works

```bash
# ──────────────────────────────────────────────
#  Bring up virtual CAN and test
# ──────────────────────────────────────────────

sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0

# Terminal 1: start the dashboard
./icsim vcan0

# Terminal 2: start the controls
./controls vcan0

# Terminal 3: watch the traffic
candump vcan0

# If you see frames scrolling — you're in business.
# Press buttons in the controls window and watch
# which arbitration IDs change in candump.
```

??? tip "Make vcan persistent across reboots"
    ```bash
    # Add to /etc/modules-load.d/vcan.conf
    echo "vcan" | sudo tee /etc/modules-load.d/vcan.conf

    # Add to /etc/network/interfaces or use a systemd unit
    cat << 'EOF' | sudo tee /etc/systemd/system/vcan0.service
    [Unit]
    Description=Virtual CAN interface vcan0
    After=network.target

    [Service]
    Type=oneshot
    RemainAfterExit=yes
    ExecStart=/sbin/ip link add dev vcan0 type vcan
    ExecStart=/sbin/ip link set up vcan0
    ExecStop=/sbin/ip link delete vcan0

    [Install]
    WantedBy=multi-user.target
    EOF

    sudo systemctl enable vcan0.service
    ```

---

## Phase 2 — Diagnostics & Protocol Testing

```bash
# ──────────────────────────────────────────────
#  UDS / DoIP / ISO-TP / SOME/IP tooling
# ──────────────────────────────────────────────
source ~/auto-lab/bin/activate

pip install udsoncan

# CaringCaribou — automotive security scanner
cd ~ && git clone https://github.com/CaringCaribou/caringcaribou.git

# gallia — Fraunhofer's automotive pentesting framework
pip install gallia

# Wireshark with automotive dissectors
sudo apt install -y wireshark tshark

# Verify scapy automotive layer
python3 -c "from scapy.contrib.automotive.uds import *; print('UDS ✓')"
python3 -c "from scapy.contrib.automotive.doip import *; print('DoIP ✓')"
python3 -c "from scapy.contrib.automotive.someip import *; print('SOME/IP ✓')"
```

---

## Phase 3 — Embedded & RE Tools

```bash
# ──────────────────────────────────────────────
#  Reverse engineering & embedded toolchain
# ──────────────────────────────────────────────

# Ghidra (download latest from ghidra-sre.org)
sudo apt install -y default-jdk
# Download: https://github.com/NationalSecurityAgency/ghidra/releases
# Extract and run: ./ghidraRun

# Emulation
sudo apt install -y qemu-system-arm qemu-user-static gdb-multiarch

# GEF — GDB exploit development extensions
bash -c "$(curl -fsSL https://gef.blah.cat/sh)"

# Firmware extraction
source ~/auto-lab/bin/activate
pip install binwalk unicorn pwntools

# Binwalk dependencies for full extraction
sudo apt install -y \
    squashfs-tools \
    mtd-utils \
    cramfsswap \
    sasquatch \
    jefferson \
    p7zip-full

# rizin — CLI reverse engineering
sudo apt install -y rizin
```

---

## Phase 4 — RF & Wireless

```bash
# ──────────────────────────────────────────────
#  SDR and wireless analysis
# ──────────────────────────────────────────────

# GNU Radio + URH
sudo apt install -y gnuradio
source ~/auto-lab/bin/activate
pip install urh

# rtl_433 — passive TPMS / key fob / ISM band decoder
sudo apt install -y rtl-433
# or build from source for latest:
# git clone https://github.com/merbanan/rtl_433 && cd rtl_433
# mkdir build && cd build && cmake .. && make && sudo make install

# WHAD — wireless hacking framework
pip install whad

# Sniffle — BLE 5 sniffer (needs TI CC26x2 Launchpad ~$30)
cd ~ && git clone https://github.com/nccgroup/Sniffle.git
```

---

## Phase 5 — EVSE / V2G

```bash
# ──────────────────────────────────────────────
#  EV charging protocol analysis
# ──────────────────────────────────────────────
source ~/auto-lab/bin/activate

# OCPP testing
pip install ocpp

# dsV2Gshark — Wireshark ISO 15118 V2G dissector
cd ~ && git clone https://github.com/dspace-group/dsV2Gshark.git
# Copy plugin to Wireshark: follow dsV2Gshark README

# EVerest Docker demo — full ISO 15118 + OCPP environment
# Requires Docker installed
cd ~ && git clone https://github.com/EVerest/everest-demo.git
# cd everest-demo && docker compose up

# HomePlugPWN — PLC attack tools (advanced)
cd ~ && git clone https://github.com/FlUxIuS/HomePlugPWN.git
```

---

## Phase 6 — Hardware Security

```bash
# ──────────────────────────────────────────────
#  Side-channel & fault injection
# ──────────────────────────────────────────────
source ~/auto-lab/bin/activate

# ChipWhisperer (software — hardware sold separately)
pip install chipwhisperer

# Jupyter notebooks for ChipWhisperer labs
cd ~ && git clone https://github.com/newaetech/chipwhisperer-jupyter.git

# OpenOCD — JTAG/SWD debug interface
sudo apt install -y openocd
```

---

## All-In-One Script

Copy this entire block to set up everything at once:

```bash
#!/bin/bash
# ═══════════════════════════════════════════════
#  automotive-cybersec-hub lab setup
#  Tested on Ubuntu 22.04+ / Kali 2024+
# ═══════════════════════════════════════════════
set -e

echo "╔═══════════════════════════════════════╗"
echo "║  automotive-cybersec-hub lab setup    ║"
echo "╚═══════════════════════════════════════╝"

echo "[1/6] System packages..."
sudo apt update && sudo apt install -y \
    build-essential git curl wget \
    can-utils libsdl2-dev libsdl2-image-dev \
    python3-pip python3-venv \
    wireshark tshark \
    qemu-system-arm qemu-user-static gdb-multiarch \
    squashfs-tools mtd-utils p7zip-full \
    gnuradio rtl-433 \
    openocd rizin default-jdk

echo "[2/6] Python venv + packages..."
python3 -m venv ~/auto-lab
source ~/auto-lab/bin/activate
pip install --upgrade pip
pip install \
    python-can cantools scapy \
    udsoncan gallia \
    binwalk unicorn pwntools \
    urh whad ocpp chipwhisperer

echo "[3/6] GEF for GDB..."
bash -c "$(curl -fsSL https://gef.blah.cat/sh)" || true

echo "[4/6] Cloning lab repos..."
mkdir -p ~/labs && cd ~/labs
[ ! -d "ICSim" ]          && git clone https://github.com/zombieCraig/ICSim.git
[ ! -d "caringcaribou" ]  && git clone https://github.com/CaringCaribou/caringcaribou.git
[ ! -d "Sniffle" ]        && git clone https://github.com/nccgroup/Sniffle.git
[ ! -d "dsV2Gshark" ]     && git clone https://github.com/dspace-group/dsV2Gshark.git
[ ! -d "everest-demo" ]   && git clone https://github.com/EVerest/everest-demo.git

echo "[5/6] Building ICSim..."
cd ~/labs/ICSim && make

echo "[6/6] Virtual CAN..."
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan 2>/dev/null || true
sudo ip link set up vcan0

echo ""
echo "╔═══════════════════════════════════════╗"
echo "║  Setup complete.                      ║"
echo "║                                       ║"
echo "║  Activate env:  source ~/auto-lab/bin/activate"
echo "║  First lab:     cd ~/labs/ICSim        ║"
echo "║                 ./icsim vcan0          ║"
echo "║  Ghidra:        download from          ║"
echo "║    ghidra-sre.org (requires JDK)       ║"
echo "╚═══════════════════════════════════════╝"
```

---

## Hardware Shopping List

No hardware needed for Phases 1–3 (virtual CAN, emulation, firmware RE). When you're ready:

### Tier 1 — CAN Bus Access (~$50)

```
┌─────────────────────────────┬────────┬──────────────────────────┐
│ Item                        │ Price  │ Why                      │
├─────────────────────────────┼────────┼──────────────────────────┤
│ CANable / CANtact           │ $25-50 │ USB-to-CAN, socketcan    │
│ OBD-II to DB9 cable         │ $10    │ Plugs into any car       │
│ Breadboard + jumper wires   │ $10    │ Bench setups             │
└─────────────────────────────┴────────┴──────────────────────────┘
```

### Tier 2 — Debug & Embedded (~$150 more)

```
┌─────────────────────────────┬────────┬──────────────────────────┐
│ Item                        │ Price  │ Why                      │
├─────────────────────────────┼────────┼──────────────────────────┤
│ Tigard (FTDI debug adapter) │ $50    │ UART/SPI/I2C/JTAG/SWD   │
│ Saleae Logic 8 (or clone)   │ $15-50 │ Protocol decode/capture  │
│ USB-UART adapter (CP2102)   │ $5     │ Serial console access    │
│ Multimeter                  │ $20    │ Voltage ID, test points  │
│ STM32 Nucleo board          │ $15-30 │ Practice target w/ SWD   │
└─────────────────────────────┴────────┴──────────────────────────┘
```

### Tier 3 — RF & Side-Channel (~$300 more)

```
┌─────────────────────────────┬────────┬──────────────────────────┐
│ Item                        │ Price  │ Why                      │
├─────────────────────────────┼────────┼──────────────────────────┤
│ RTL-SDR Blog V4             │ $30    │ Receive-only SDR         │
│ TI CC26x2 Launchpad         │ $30    │ Sniffle BLE sniffer      │
│ Flipper Zero                │ $170   │ Sub-GHz/RFID/NFC recon   │
│ Proxmark3 Easy              │ $50-80 │ RFID/NFC deep testing    │
│ ChipWhisperer Nano          │ $50    │ Entry SCA + glitching    │
└─────────────────────────────┴────────┴──────────────────────────┘
```

### Tier 4 — Full Lab (~$500+ more)

```
┌─────────────────────────────┬────────┬──────────────────────────┐
│ Item                        │ Price  │ Why                      │
├─────────────────────────────┼────────┼──────────────────────────┤
│ HackRF One                  │ $300   │ TX + RX SDR              │
│ ChipWhisperer Husky         │ $500   │ Full SCA + FI platform   │
│ J-Link EDU                  │ $60    │ JTAG/SWD probe           │
│ Junkyard ECU                │ $20-100│ Real target hardware     │
│ Bench power supply (0-30V)  │ $50-80 │ Power ECUs safely        │
└─────────────────────────────┴────────┴──────────────────────────┘
```

??? tip "Where to find cheap ECUs"
    - **Pick-n-pull junkyards** — body control modules, instrument clusters ($10-30)
    - **eBay / car-part.com** — search by OEM part number
    - **HardPWN / DEF CON CHV** — sometimes give away targets
    - **Open Garages community** — members sell surplus hardware

??? tip "Bench power supply notes"
    Most automotive ECUs expect 12V nominal (9–16V range). Use a bench supply, not a wall adapter. Confirm the pinout before applying power — wrong voltage to the wrong pin will destroy the ECU instantly. Start with a current limit of 500mA and increase as needed.

---

## Verify Your Setup

Run this after install to confirm everything is working:

```bash
source ~/auto-lab/bin/activate

echo "=== Checking tools ==="
which candump       && echo "✓ can-utils"       || echo "✗ can-utils"
which wireshark     && echo "✓ wireshark"       || echo "✗ wireshark"
which qemu-arm      && echo "✓ qemu-arm"        || echo "✗ qemu-arm"
which gdb-multiarch && echo "✓ gdb-multiarch"   || echo "✗ gdb-multiarch"
which openocd       && echo "✓ openocd"         || echo "✗ openocd"
which rizin         && echo "✓ rizin"           || echo "✗ rizin"

echo "=== Checking Python packages ==="
python3 -c "import can"        && echo "✓ python-can"    || echo "✗ python-can"
python3 -c "import cantools"   && echo "✓ cantools"      || echo "✗ cantools"
python3 -c "import scapy"     && echo "✓ scapy"         || echo "✗ scapy"
python3 -c "import udsoncan"  && echo "✓ udsoncan"      || echo "✗ udsoncan"
python3 -c "import binwalk"   && echo "✓ binwalk"       || echo "✗ binwalk"
python3 -c "import unicorn"   && echo "✓ unicorn"       || echo "✗ unicorn"
python3 -c "import chipwhisperer" && echo "✓ chipwhisperer" || echo "✗ chipwhisperer (expected if no hw)"

echo "=== Checking vcan0 ==="
ip link show vcan0 2>/dev/null && echo "✓ vcan0 is up"  || echo "✗ vcan0 not found (run: sudo modprobe vcan)"

echo "=== Checking lab repos ==="
[ -d ~/labs/ICSim ]         && echo "✓ ICSim"          || echo "✗ ICSim"
[ -d ~/labs/caringcaribou ] && echo "✓ CaringCaribou"  || echo "✗ CaringCaribou"
[ -d ~/labs/Sniffle ]       && echo "✓ Sniffle"        || echo "✗ Sniffle"

echo "=== Done ==="
```
