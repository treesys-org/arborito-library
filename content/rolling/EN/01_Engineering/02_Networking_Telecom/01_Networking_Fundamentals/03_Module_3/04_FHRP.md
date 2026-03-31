@title: FHRP: HSRP, VRRP, and GLBP
@icon: ⚖️
@description: Redundant default gateway, priority, preempt, tracking.
@order: 4

# First hop redundancy: the gateway is not a SPOF

**HSRP** (Cisco), **VRRP** (IETF standard), and **GLBP** (Cisco active-active) provide a **virtual IP** shared by routers so hosts use **one default gateway** resilient to failure. This lesson covers **priority**, **preempt**, **tracking**, and **L2 considerations**.

@section: Virtual IP/MAC

Hosts point to a **VIP**; the **master/active** answers ARP. If it fails, another member takes over after **hello timers**.

**Preempt:** higher priority router reclaims role when back (evaluate stability impact).

@section: Tracking

**Object tracking** (IP SLA interfaces) lowers priority if the WAN uplink fails, forcing failover even if the LAN side is up.

@section: Split-brain

If **L2** between FHRP routers partitions, **two actives** can cause **duplicate IP** or MAC flapping. **BFD** and solid L2 design mitigate.

@section: Quick comparison

| Proto | Vendor | Load sharing |
|-------|--------|--------------|
| HSRP | Cisco | Active/standby typical |
| VRRP | Standard | Active/standby |
| GLBP | Cisco | Active/active per ARP |

@section: Common mistakes

* **Same priority** and conflicting virtual MAC.
* Not aligning **timers** with STP convergence.

@section: Suggested lab

1. Configure HSRP/VRRP between two L3 routers on a VLAN.
2. Cut the active uplink and observe failover with continuous ping.
3. Enable WAN interface tracking simulation.

@quiz: What problem does an FHRP solve on the LAN?
@option: Outbound NAT
@correct: Redundancy for the first-hop default gateway when a router fails
@option: Wi‑Fi encryption
