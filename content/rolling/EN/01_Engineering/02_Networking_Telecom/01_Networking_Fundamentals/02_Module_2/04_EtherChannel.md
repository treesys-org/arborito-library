@title: EtherChannel / LAG: Link Aggregation
@icon: 🔗
@description: LACP vs PAgP, load balancing, layer 2 vs layer 3.
@order: 4

# Link aggregation: more bandwidth and redundancy

**EtherChannel** (Cisco) or **LAG** (802.3ad **LACP**) bundles multiple physical links into a **logical channel** so **STP** does not block all but one. It also enables **per-flow load balancing** (hash). This lesson compares **LACP** vs **PAgP**, **L2 vs L3**, and pitfalls.

@section: LACP vs static

**LACP** negotiates active/standby members; **mode on** is static without negotiation (risky if the peer differs).

**PAgP** is Cisco proprietary; prefer **LACP** multi-vendor.

@section: Load balancing

Hashes typically use **src/dst MAC**, **IP**, **ports** depending on configuration. A single flow **cannot** exceed one member’s speed; many flows spread load.

@section: L3 EtherChannel

**Routed** interfaces in a **port-channel** for L3: OSPF/BGP neighbors over the bundle.

@section: Common mistakes

* **Speed/duplex mismatch** across members.
* **Allowed VLAN** mismatch on trunk members of the channel.

@section: Suggested lab

1. Create a Port-channel between two switches with active/passive LACP.
2. Run `show etherchannel summary` and verify flags.
3. Generate multiple iperf flows and observe distribution.

@quiz: Which open standard is commonly used to negotiate link aggregation between switches?
@option: PAgP
@correct: LACP (802.3ad)
@option: DTP
