@title: Layer-2 Security: DHCP Snooping, DAI, and 802.1X
@icon: 🛡️
@description: Spoofing attacks, switch mitigations, and port access control.
@order: 5

# Layer-2 security: attackers on the same LAN

On the LAN, **ARP spoofing**, **rogue DHCP**, **MAC flooding**, and **VLAN hopping** are classics. Modern switches provide **DHCP snooping**, **DAI**, **IP source guard**, and **802.1X** to contain abuse. This lesson summarizes threats and mitigations.

@section: DHCP snooping

Mark **trusted** ports (toward DHCP servers) vs **untrusted**. Drop **OFFER/ACK** on untrusted ports; build a **binding table** (MAC/IP/VLAN/port).

**IP Source Guard** uses that table to filter IP traffic.

@section: DAI (Dynamic ARP Inspection)

Validates ARP against DHCP snooping or static ACLs; blocks common **ARP spoofing**.

**Rate limiting** helps against floods.

@section: 802.1X

**Port-based access control** with EAP: **supplicant** on host, **authenticator** on switch, **RADIUS** backend.

**MAB** (MAC Authentication Bypass) for devices without 802.1X (printers).

@section: Storm control

Limits **broadcast/multicast/unknown unicast** by percentage or pps.

@section: Common mistakes

* DHCP snooping without correct **trusted** ports → clients never get IP.
* Strict 802.1X without a **guest VLAN** for emergencies.

@section: Suggested lab

1. Enable DHCP snooping on a test VLAN and observe bindings.
2. Simulate a rogue DHCP server and verify drops.
3. Document a phased 802.1X rollout plan.

@quiz: What attack does DHCP snooping mainly mitigate?
@option: Layer-7 DDoS
@correct: Unauthorized DHCP servers on the LAN
@option: DNS exfiltration
