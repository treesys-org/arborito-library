@title: OSI and TCP/IP Models: Layer by Layer
@icon: 📚
@description: Encapsulation, PDUs, ports, and a mental map for troubleshooting.
@order: 1

# OSI and TCP/IP: the map that orders the chaos

The **OSI** (7 layers) and **TCP/IP** (4 practical layers) models are not certification trivia: they are **shared vocabulary** to locate failures — physical? IP? TCP? application? This lesson fixes **encapsulation**, **PDUs**, and how to move between models without vaguely saying “layer 4” when you mean “ports.”

@section: Why two models

**OSI** splits finely (presentation and session layers). **TCP/IP** groups what we usually implement together (kernel stack, sockets). In troubleshooting you still say “this smells like **layer 2**” (STP/VLAN) vs “**layer 3**” (routing) vs “**layer 7**” (HTTP/DNS).

@section: OSI in an operational table

| Layer | Name | PDU / unit | Examples |
|-------|------|------------|----------|
| 7 | Application | Data | HTTP, DNS, SSH |
| 4 | Transport | Segment | TCP, UDP, ports |
| 3 | Network | Packet | IP, ICMP, routes |
| 2 | Data link | Frame | Ethernet, MAC, VLAN tag |
| 1 | Physical | Bits | cable, fiber, encoding |

Layers 5–6 in OSI are often folded into “application” in TCP/IP.

@section: Encapsulation

Each layer adds **headers** (and sometimes a trailer) to the payload. ICMP rides **inside** IP, inside Ethernet. In **Wireshark** you expand the tree and see exactly which field is wrong (checksum, TTL, MAC, etc.).

@section: TCP/IP mapping to reality

* **Link:** Ethernet, Wi‑Fi, ARP.
* **Internet:** IPv4/IPv6, ICMP, routing.
* **Transport:** TCP (reliability, window), UDP (best effort).
* **Application:** protocols on top of sockets.

**Socket** = IP + port + protocol (TCP/UDP).

@section: Ports and services

**Ports** multiplex at the **transport** layer (TCP/UDP), not “layer 7” by themselves. `443/tcp` is the conventional HTTPS port unless configured otherwise.

@section: Layer-guided diagnosis

1. **Layers 1–2:** link down, FCS errors, STP blocking ports.
2. **Layer 3:** no route, wrong subnet, L3 firewall.
3. **Layer 4:** TCP not established (retransmitted SYN), closed port.
4. **Layer 7:** HTTP 502, DNS NXDOMAIN, invalid TLS certificate.

Do not jump to “it’s the application” without checking **IP connectivity** and **DNS**.

@section: Common mistakes

* Calling everything “layer 7” because a user sees it.
* Confusing **layer-2 broadcast** with **IP multicast**.
* Ignoring that **NAT** breaks end-to-end at layers 3/4.

@section: Suggested lab

1. On Linux run `ss -tn` and `ip route` while opening an HTTPS site; relate sockets to the default route.
2. Capture a TCP handshake with `tcpdump` or Wireshark and label IP/TCP headers.
3. Draw ICMP encapsulated in IP in Ethernet for a ping.

@quiz: In the OSI model, which layer primarily uses MAC addresses?
@option: Layer 3
@correct: Layer 2 (data link)
@option: Layer 7
