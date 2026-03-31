@title: OSPFv2: Areas, LSDB, and Neighbors
@icon: 🔄
@description: LSA types, DR on broadcast, cost, and inter-area filtering.
@order: 3

# OSPF: link-state routing for enterprises

**OSPF** (v2 for IPv4, v3 for IPv6) is a **link-state** protocol with hierarchical **areas**, a synchronized **LSDB**, and **SPF** path computation. This lesson introduces **neighbors**, **area types**, **bandwidth-based metrics**, and basic troubleshooting.

@section: Neighbors and states

Neighbors on point-to-point links; on **broadcast** segments elect **DR/BDR** to reduce adjacencies. **Full** state is required to exchange LSAs.

**Hello/dead timers** must match (or be compatible per tuning).

@section: Areas and ABR

**Area 0 (backbone)** connects other areas via **ABRs**. Different **LSA** types by scope; **summarization** between areas shrinks tables.

**Stub/NSSA** limit external routes for simpler sites.

@section: Metric

**Cost** = reference bandwidth / interface bandwidth (Cisco default). Adjust **auto-cost** or **interface cost** to influence SPF.

@section: Authentication

**Plain text** (bad), **MD5**, **SHA** depending on platform. Protects against rogue routers on the segment.

@section: Common mistakes

* **MTU mismatch** on OSPF → stuck neighbors.
* Wrong **area** on an interface.
* **Passive-interface** on the wrong side.

@section: Suggested lab

1. Build OSPF on three routers: backbone + stub area.
2. Inspect `show ip ospf neighbor` and a summarized LSDB view.
3. Change costs and verify preferred path changes.

@quiz: What is the DR’s role on an OSPF broadcast segment?
@option: Replace BGP
@correct: Reduce full adjacencies between all routers on the same subnet
@option: Encrypt all IP traffic
