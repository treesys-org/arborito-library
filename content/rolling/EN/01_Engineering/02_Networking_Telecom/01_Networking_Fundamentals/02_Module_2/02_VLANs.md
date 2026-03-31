@title: VLANs: Segmentation and Layer-2 Design
@icon: 🎭
@description: Broadcast domains, 802.1Q, inter-VLAN routing, troubleshooting.
@order: 2

# VLANs: controlled broadcast domains

A **VLAN** (`802.1Q`) segments **broadcast domains** logically over the same physical infrastructure. Good VLAN design reduces **broadcast storms**, isolates **guests** and **VoIP**, and prepares **QoS** per queue. This lesson covers tagging, **inter-VLAN routing**, and typical mistakes.

@section: 802.1Q tagging

Frames may carry a **tag** with **VLAN ID** (12 bits). Hosts usually send **untagged** traffic; the switch applies **PVID** on access ports.

**Q-in-Q** (stacked VLAN) in providers: double tag; uncommon in typical access.

@section: Inter-VLAN routing

To communicate between VLANs you need a **router** or **L3 switch** (SVI). Without it, hosts in different VLANs cannot talk even if they share physical context elsewhere.

**Router-on-a-stick:** 802.1Q subinterfaces on a router. **L3 switch:** hardware routing.

@section: Special VLANs

* **Black hole** VLAN for discarded traffic (do not rely on default VLAN1).
* **Voice VLAN** with CDP/LLDP for IP phones (vendor dependent).

@section: Troubleshooting

* Wrong access VLAN → no DHCP or wrong gateway.
* **Trunk** `allowed vlan` list too tight → filtered traffic.

@section: Common mistakes

* Undocumented **VLAN databases** across teams.
* Changing **native VLAN** without updating all ends.

@section: Suggested lab

1. Create VLAN 10 and 20; assign hosts.
2. Configure SVI or router-on-a-stick and validate cross VLAN pings.
3. Filter with `vlan allowed` on the trunk and observe failure.

@quiz: What problem do VLANs mainly solve?
@option: Increase Ethernet speed
@correct: Segment broadcast domains and layer-2 policies
@option: Remove the need for IP
