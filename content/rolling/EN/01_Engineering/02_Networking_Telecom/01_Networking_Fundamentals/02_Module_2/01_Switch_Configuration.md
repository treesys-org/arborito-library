@title: Switch Configuration: VLAN 1, Management, and Access Ports
@icon: 🖧
@description: Basic IOS/NX-OS, management VLAN, 802.1Q trunks, and hygiene.
@order: 1

# Layer-2 switch configuration: operational baseline

**Switches** manage collision domains and **VLANs** to segment broadcast domains. This lesson covers **access ports**, **802.1Q trunks**, **management VLAN**, and admin hygiene (SSH, AAA, avoid using VLAN1 for sensitive data).

@section: Port modes

* **Access:** single user VLAN; **untagged** frames from hosts with **PVID** on the switch.
* **Trunk:** carries multiple VLANs with **802.1Q tagging**; **native VLAN** untagged (must match both ends).

**DTP** (Cisco) can negotiate trunks; often disabled in secure designs.

@section: Management VLAN

Assign an **SVI** (VLAN interface) or equivalent IP for **SSH/SNMP**. Separate **management** and **data** planes when possible (out-of-band, light VRF use).

The **management gateway** must be reachable via admin routing.

@section: Spanning tree (preview)

Before creating physical loops, **STP** must run. Trunk miswiring can create **loops** if you connect two switches with two cables without **EtherChannel** or STP.

@section: Basic security

* **BPDU Guard** on access ports.
* **Port-security** to limit MACs on user ports.
* **SSH** v2, not telnet.

@section: Common mistakes

* **Native VLAN** mismatch on a trunk → **VLAN hopping** or crossed traffic.
* Management sharing a user VLAN without ACLs.

@section: Suggested lab

1. Configure two VLANs (data/VoIP) and a trunk between two lab switches.
2. Verify inter-VLAN reachability via router-on-a-stick or L3 switch.
3. Show MAC tables and spanning-tree state.

@quiz: What is a trunk port in switched Ethernet?
@option: A port without cable
@correct: A port carrying multiple tagged 802.1Q VLANs and defining a native VLAN
@option: A 10 Mbps-only port
