# Awesome Automotive Security

A curated, opinionated learning path for automotive cybersecurity.

This is not a flat link dump. It is a sequenced path with labs you can do today, built for security practitioners entering automotive and complete beginners breaking into the field. Where someone else has already written the definitive resource, we link to it.

**[Start with the Roadmap →](roadmap.md)**

---

## Acknowledgments

This resource exists because these people put their knowledge out in the open. If you learn from this site, you're learning from them.

- **[Willem Melching](https://icanhack.nl/)** — The [icanhack.nl Knowledge Base](https://icanhack.nl/knowledge-base/networks/introduction/) is the single best free resource on vehicle networks, diagnostics, and automotive RE. His blog posts on real ECU firmware reversing set the bar for the field. This site links to his work constantly because there's no point rewriting what he's already done better.
- **[Matthew Alt / Wrong Baud (VoidStar Security)](https://voidstarsec.com/)** — The [VoidStar Embedded Security Roadmap](https://voidstarsec.com/roadmap/) and [Wrong Baud's Blog](https://wrongbaud.github.io/) are the definitive open resources for hardware hacking and embedded RE. The UART → SPI → JTAG → Ghidra → fault injection progression that this site's embedded track follows is his. His Ghidra training course through Hackaday U has taught thousands of people to reverse firmware.
- **[Nils Weiss](https://github.com/polybassa)** — Built and maintains the [Scapy automotive layer](https://scapy.readthedocs.io/en/latest/layers/automotive.html) — the most complete open-source automotive protocol testing framework. UDS, DoIP, ISO-TP, SOME/IP, HSFZ, GMLAN, XCP, plus automated scanners and stateful fuzzers. His Troopers talks are essential viewing.
- **[Colin O'Flynn (NewAE Technology)](https://www.newae.com/)** — Created [ChipWhisperer](https://github.com/newaetech/chipwhisperer), making side-channel analysis and fault injection accessible to anyone. His BAM BAM!! paper demonstrated EMFI on a real automotive ECU, bridging the gap between lab research and in-situ vehicle attacks.
- **[Xeno Kovah (OpenSecurityTraining2)](https://ost2.fyi/)** — Founded OpenSecurityTraining in 2011 and relaunched it as OST2 in 2021. A 501(c)(3) nonprofit providing the world's deepest free cybersecurity training — x86 assembly, OS internals, firmware security, RISC-V, coreboot, QEMU internals, and more. The architecture courses are essential prerequisites for anyone doing embedded RE at depth.
- **[Sébastien Dudek](https://github.com/FlUxIuS)** — The [V2G Injector](https://www.sstic.org/2019/presentation/v2g_injector_playing_with_electric_cars_and_charging_stations_via_powerline/) research and [HomePlugPWN](https://github.com/FlUxIuS/HomePlugPWN) tools opened up EV charging security as a research field.
- **[Craig Smith](http://opengarages.org/)** — The [Car Hacker's Handbook](http://opengarages.org/handbook/) (free) is still the first book most people read on automotive security. [ICSim](https://github.com/zombieCraig/ICSim) is where almost everyone's CAN bus journey starts.
- **Charlie Miller & Chris Valasek** — Their work from 2013–2015 (DEF CON 21 → Jeep Cherokee remote exploit) put automotive security on the map and led to the first major vehicle recall driven by security research.
- **[Samy Kamkar](https://samy.pl/)** — RollJam, OpenSesame, and a body of automotive RF work that demonstrated real-world key fob and rolling code vulnerabilities.

And the communities that keep this field alive: [Open Garages](http://opengarages.org/), [DEF CON Car Hacking Village](https://www.carhackingvillage.com/), [ASRG](https://www.asrg.io/), and everyone who publishes their research openly.

---

## Quick links

| I want to… | Go here |
|---|---|
| See the full learning path | [Roadmap](roadmap.md) |
| Sniff CAN frames in 30 minutes | [CAN Bus](knowledge-base/01-can.md) |
| Understand vehicle protocol stacks | [icanhack.nl Knowledge Base](https://icanhack.nl/knowledge-base/networks/introduction/) |
| Learn embedded hacking from scratch | [Embedded Systems](knowledge-base/03-embedded.md) |
| Set up a hardware lab | [VoidStar Security Roadmap](https://voidstarsec.com/roadmap/) |
| Hack EV chargers | [EVSE & Charging](knowledge-base/05-evse.md) |
| Find a CTF | [CTFs & Challenges](resources/ctfs.md) |

---

!!! tip "Disclaimer"
    Everything here is publicly available research and tooling for education and authorized testing.
