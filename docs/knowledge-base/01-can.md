# 1. CAN Bus

CAN is the backbone of nearly every vehicle. Start here.

## Read first

- [icanhack.nl — Controller Area Network](https://icanhack.nl/knowledge-base/networks/controller-area-network/) — protocol deep dive
- [icanhack.nl — Secure Onboard Communication (SecOC)](https://icanhack.nl/knowledge-base/networks/secure-onboard-communication/) — CAN message authentication
- [CSS Electronics — CAN Bus Intro](https://www.csselectronics.com/pages/can-bus-simple-intro-tutorial) — plain-English explainer with diagrams
- [Car Hacker's Handbook Ch. 2](http://opengarages.org/handbook/) — CAN from a security perspective
- [Bosch CAN Specification 2.0](https://www.kvaser.com/software/7330130980914/V1/can2spec.pdf) — the original spec (skim for reference)

## Tools

| Tool | Purpose | Link |
|---|---|---|
| can-utils | `candump`, `cansend`, `cangen`, `cansniffer` | [linux-can/can-utils](https://github.com/linux-can/can-utils) |
| ICSim | Instrument cluster simulator on virtual CAN | [zombieCraig/ICSim](https://github.com/zombieCraig/ICSim) |
| SavvyCAN | GUI analyzer with DBC support | [collin80/SavvyCAN](https://github.com/collin80/SavvyCAN) |
| python-can | Python CAN library | [hardbyte/python-can](https://github.com/hardbyte/python-can) |
| cantools | DBC parsing and signal encoding/decoding | [eerimoq/cantools](https://github.com/eerimoq/cantools) |

For CAN adapters and analysis tools, see also [icanhack.nl — CAN Adapters](https://icanhack.nl/knowledge-base/tools/can-adapters/) and [icanhack.nl — CAN Analysis](https://icanhack.nl/knowledge-base/tools/can-analysis/).

## Lab

Set up a virtual CAN interface and run ICSim:

```bash
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
```

Run `icsim vcan0` and `controls vcan0`, then use `candump` and `cansniffer` to identify arbitration IDs. Replay with `cansend`. Write a Python script to automate it.

See the [Roadmap — Phase 1](../roadmap.md#phase-1-can-bus-fundamentals) for the full exercise.
