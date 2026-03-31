@title: Structured Cabling: Categories, Testing, and Practice
@icon: 🔌
@description: Cat5e/6/6A, fiber, panels, PoE, and link certification.
@order: 2

# Structured cabling: the physical layer everything rests on

Before advanced routing, the **physical layer** must be reliable: **twisted-pair** categories, **fiber** for long spans or backbone, **patch panels** kept tidy, and **tests** with a certifier or at least **wiremap**. This lesson summarizes **Cat** ratings, **PoE**, and installation mistakes that cause **intermittent** errors (worse than a clean cut).

@section: Copper categories

* **Cat5e:** up to 1 Gbps over typical 100 m runs; still common in older LANs.
* **Cat6 / 6A:** better crosstalk; 6A targets **10 Gbps** over longer distances than classic Cat6.
* **Cat8:** short-reach data centers, very high speed.

**AWG** and **shielding** (U/FTP, F/FTP) matter in noisy industrial environments.

@section: Fiber optics

**Single-mode (SM)** for long distances and MAN/WAN; **multimode (MM)** for campus. **LC** connectors dominate in racks; **clean ferrules** are critical (dust = attenuation).

**Attenuation and ORL** are measured with an **OTDR** on long links.

@section: Standards and permanent link

**ISO/IEC 11801**, **TIA-568** define **channel** vs **permanent link**. A mis-patched cord in the rack may pass **continuity** and fail **NEXT** under real traffic.

@section: PoE (Power over Ethernet)

**802.3af/at/bt** power APs, cameras, IP phones. **Current** limits per cable and effective **length**: thin cable → voltage drop.

**Good practice:** switches with enough PoE budget; plan mixed loads.

@section: Minimum tests

* **Wiremap** (continuity and pairs).
* **Length** estimate via TDR on certifiers.
* For **gigabit**, frequency certification (e.g. 250 MHz for Cat6 depending on case).

@section: Common mistakes

* Violating **fiber bend radius** → loss.
* **Cable trays** sharing high-voltage without separation → crosstalk.
* **Unlabeled patch panels** → hours lost in troubleshooting.

@section: Suggested lab

1. Practice RJ45 crimping (if you have tools) and verify with a tester.
2. Compute a simple fiber link budget: transceiver power − losses − margin.
3. Document an **IDF/MDF** diagram for a fictional floor.

@quiz: What typical problem do dirty fiber connectors cause?
@option: Higher speed automatically
@correct: Attenuation and reflections that degrade or make the link intermittent
@option: More bandwidth automatically
