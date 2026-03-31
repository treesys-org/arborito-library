@title: SDN: Separating Control and Data Planes
@icon: ☁️
@description: OpenFlow, controllers, VXLAN overlays, operational trade-offs.
@order: 4

# SDN: program the network from a controller

**SDN** separates the **control plane** (policy, logic) from the **data plane** (forwarding). Implementations range from pure **OpenFlow** to **SD-WAN** and **VXLAN/EVPN** fabrics. This lesson anchors concepts without vendor lock-in.

@section: Control vs data plane

**OpenFlow** switches consult a **controller** to install flows. Advantage: centralized innovation; risk: **controller availability** and **TCAM** scale.

@section: Overlays

**VXLAN** encapsulates L2 over UDP/IP (VNI). **EVPN** distributes MAC/IP via BGP — popular in **leaf-spine** data centers.

**SD-WAN** overlays multiple transports (MPLS, Internet, LTE).

@section: Automation

**Intent-based networking** declares outcomes; controllers translate to low-level config. Requires **data models** (YANG) and stable **APIs**.

@section: Trade-offs

* **Vendor lock-in** of the controller.
* **Debugging** is more abstract (tunnel vs underlay).

@section: Common mistakes

* Insufficient **MTU** for overlays (hidden fragmentation).
* Under-provisioned SD-WAN **head-end** encryption throughput.

@section: Suggested lab

1. Read a **spine-leaf** EVPN diagram and label BGP roles.
2. Compare SD-WAN vs traditional MPLS for 20 branches.
3. List metrics to monitor (tunnel latency, underlay loss).

@quiz: Which encapsulation is common in modern data-center overlays?
@option: PPPoE
@correct: VXLAN (MAC-in-UDP)
@option: Frame Relay
