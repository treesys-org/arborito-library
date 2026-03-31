@title: STP/RSTP: Preventing Broadcast Storms
@icon: 🌳
@description: Root bridge, port costs, states, and protections (BPDU Guard, Root Guard).
@order: 3

# Spanning Tree: redundancy without control kills the LAN

**STP** (802.1D) and **RSTP** (802.1w) prevent **layer-2 loops** by blocking redundant ports. Without STP, **broadcasts** multiply until the LAN collapses. This lesson explains **root bridge**, **costs**, **states**, and **protections** (BPDU Guard, Root Guard).

@section: Root idea

A **root bridge** is elected (bridge ID). Each switch computes a **root port** (best path to root) and **designated ports**; other ports are **blocking/discarding**.

**Costs** depend on speed; faster paths are preferred.

@section: RSTP vs classic STP

**RSTP** converges faster (seconds vs tens of seconds). **MST** (802.1s) groups VLANs into instances for better load balancing.

@section: Protections

* **BPDU Guard:** disables an access port if it receives BPDUs (likely a loop).
* **Root Guard:** prevents an unexpected root on a designated port.
* **Loop Guard:** protects against unidirectional links.

@section: Portfast

**Edge port** for end hosts: skips listening/learning. **Never** on links to other switches.

@section: Common mistakes

* Connecting two switches with two cables **without** STP/LAG → loop.
* Mis-tuned **timers** in large networks (less common with RSTP).

@section: Suggested lab

1. Connect two switches with two cables and observe STP blocking.
2. Add EtherChannel and compare convergence.
3. Enable BPDU Guard on an access port and connect a small switch → verify err-disable.

@quiz: What layer-2 condition does STP try to prevent?
@option: Lack of IPv6
@correct: Loops that cause broadcast storms
@option: TCP packet loss
