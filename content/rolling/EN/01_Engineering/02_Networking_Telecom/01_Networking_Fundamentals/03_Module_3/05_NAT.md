@title: NAT and PAT: Address Translation at the Edge
@icon: 🔄
@description: SNAT/DNAT, PAT overload, hairpin NAT, application implications.
@order: 5

# NAT/PAT: sharing public IPv4 without magic

**NAT** translates **private↔public** addresses. **PAT** (NAPT) multiplexes many internal hosts on **one public IP** using **ports**. Essential for IPv4; breaks **end-to-end** and complicates **VoIP**, **IPsec**, **FTP** without **ALGs** or helpers. This lesson covers **SNAT/DNAT**, **hairpin**, **NAT444**, and troubleshooting.

@section: SNAT vs DNAT

* **SNAT (masquerade):** outbound Internet uses the router’s public IP.
* **DNAT (port forward):** public `203.0.113.5:443 → 10.0.0.10:443`.

**Stateful** NAT keeps session tables.

@section: PAT

Thousands of flows share one public IP; table key is unique `(src IP, src port)` per flow.

**Port exhaustion** in massive CGNAT → issues for very chatty apps.

@section: Hairpin NAT

An internal client hits the **public VIP** that DNATs to an internal server: requires **NAT loopback/hairpin** on the router/firewall.

@section: NAT and security

NAT is **not** a firewall by itself; it is **obfuscation**. Use explicit **stateful** firewalling.

@section: Common mistakes

* **Asymmetric routing** across multiple ISPs with inconsistent NAT.
* Disabled **ALG** breaking FTP/SIP in some cases.

@section: Suggested lab

1. Configure SNAT on a Linux router (`iptables`/`nft`) or lab firewall.
2. Publish an internal web server with DNAT and test from outside.
3. Test hairpin from the LAN if supported.

@quiz: What is PAT (NAPT) mainly?
@option: DNS name translation
@correct: NAT with port overload to multiplex many sessions on one public IP
@option: TLS encryption
