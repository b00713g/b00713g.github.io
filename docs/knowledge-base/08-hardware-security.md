# 8. Hardware Security

Side-channel analysis, fault injection, secure boot bypass on automotive ECUs.

## Read first

- [icanhack.nl — Fault Injection](https://icanhack.nl/knowledge-base/existing-research/fault-injection/)
- [BAM BAM!! — EMFI on Automotive ECUs (O'Flynn, escar 2020)](https://eprint.iacr.org/2020/937.pdf) — secure boot bypass on a real ECU
- [VoidStar — Replicant: Fault Injection on Trezor One](https://voidstarsec.com/blog/replicant-part-1) — voltage glitching methodology
- [VoidStar — Glitching in 3D: Low Cost EMFI (CanSecWest 2024)](https://voidstarsec.com/csw-2024)
- [ChipWhisperer Jupyter Notebook Labs](https://chipwhisperer.readthedocs.io/en/latest/getting-started.html) — the best free hardware security course
- [HardwareAllTheThings](https://github.com/swisskyrepo/HardwareAllTheThings) — comprehensive reference
- [NXP i.MX SDP_READ_DISABLE Fuse Bypass (CVE-2022-45163) — NCC Group](https://research.nccgroup.com/2022/11/17/cve-2022-45163/) — real-world fuse bypass on automotive-adjacent NXP silicon

## Tools

| Tool | Purpose | Link |
|---|---|---|
| ChipWhisperer | Power analysis + voltage/clock glitching platform | [newaetech/chipwhisperer](https://github.com/newaetech/chipwhisperer) |
| ChipSHOUTER | Electromagnetic fault injection | [NewAE Technology](https://www.newae.com/chipSHOUTER) |
| PicoGlitcher | Low-cost RP2040 voltage glitcher | [MKesenheimer/PicoGlitcher](https://github.com/MKesenheimer/PicoGlitcher) |
| findus | Fault injection automation library | [MKesenheimer/findus](https://github.com/MKesenheimer/findus) |

## Getting started

1. ChipWhisperer Jupyter notebooks — power analysis → SPA on password → CPA on AES → voltage glitching
2. [RHme CTF challenges](https://github.com/Riscure/Rhme-2016) — side-channel and fault injection on Arduino
3. The BAM BAM paper to understand how these techniques apply to real automotive ECUs
