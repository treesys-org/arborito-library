@title: Ethernet: Frames, MACs, and Switching
@icon: 📡
@description: Frame format, collision vs broadcast domains, switching, and MTU.
@order: 3

# Ethernet: from frames to switches

**Ethernet** is the dominant LAN family. Understanding **frames**, **MAC addresses**, **layer-2 switching**, and **MTU** prevents confusion when mixing **VLANs**, **LAG**, and **jumbo frames**. This lesson bridges physical cabling and **IPv4** that follows.

@section: Ethernet frame (simplified)

Key fields: **destination/source MAC**, **EtherType/Length**, payload, **FCS** (CRC).  
**MTU** is typically 1500 bytes on classic Ethernet; **jumbo** (9000) only if **every** hop supports it.

@section: MAC addresses

48 bits, **unicast** vs **multicast** (e.g. STP uses 01:80:c2:…). **U/L** and **I/G** bits matter when reading traces.

**CAM table** on switches learns MAC per port; **flooding** if unknown (within that VLAN’s broadcast domain).

@section: Switching vs hubs

Modern **switches** remove **collision domains** per port (full duplex). Hubs (obsolete) shared the medium → collisions.

**Broadcast domain:** still per **VLAN** (and across all L2 if you do not segment).

@section: Autonegotiation

**Speed/duplex** negotiate; **manual vs auto mismatch** causes collisions or poor performance. Document both ends on critical links.

@section: Flow control (optional)

**802.3x PAUSE** can briefly pause a sender; misused it can cause **head-of-line blocking** in some designs. Many data centers disable it and manage congestion higher up.

@section: Common mistakes

* **Layer-2 loops** without STP → broadcast storms.
* **Inconsistent MTU** → IP fragmentation or PMTUD black holes.
* Confusing **MAC flapping** (duplicate cable / loop) with routing issues.

@section: Suggested lab

1. On a lab switch, watch the **MAC table** before and after pings between two hosts.
2. Change MTU on Linux (`ip link set dev eth0 mtu …`) and measure behavior with **do-not-fragment** ping.
3. Capture ARP traffic and relate it to next-hop resolution.

@quiz: Which device learns MAC addresses and maps them to ports?
@option: Routers only
@correct: Layer-2 switches (CAM table)
@option: DHCP servers only
