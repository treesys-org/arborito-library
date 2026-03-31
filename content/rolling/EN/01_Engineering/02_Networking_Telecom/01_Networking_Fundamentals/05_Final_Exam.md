
@title: Certification Exam: Networking fundamentals
@exam
@icon: 🌐
@description: OSI/TCP models, switching, routing, and policy. Pass the majority to certify the track.
@order: 5

# Final exam: Networking fundamentals

Four blocks: lower layers and addressing, switching, routing, advanced services.

> **Instructions:** Single best answer per question; majority correct to pass.


## Block 1: Models, Ethernet, IP (module 1)

@quiz: On which OSI layer do classic Layer 2 switches primarily operate?
@option: Layer 3.
@correct: Layer 2 (data link).
@option: Layer 7 only.
@option: Layer 1 only over fiber.

@quiz: Which PDU is commonly tied to the transport layer in OSI terminology?
@option: Frame.
@correct: Segment / datagram depending on protocol (e.g., TCP segment).
@option: Always an IP packet.
@option: Raw unstructured bits.

@quiz: Which 6-byte field identifies a node on Ethernet?
@option: IPv6 interface ID only.
@correct: MAC address.
@option: Autonomous system number.
@option: VLAN ID.

@quiz: What does an IPv4 /24 mask imply?
@option: A 32-bit network prefix.
@correct: A 24-bit network prefix (256 theoretical host addresses in the block).
@option: DHCP-only hosts.
@option: No subnetting possible.

@quiz: Which protocol resolves known IPv4 addresses to MACs on a LAN?
@option: DNS.
@correct: ARP.
@option: ICMP only.
@option: OSPF.

@quiz: IPv6 link-local addresses typically:
@option: Are globally routable by default.
@correct: Stay on the local link (limited scope).
@option: Replace Layer 2 MACs.
@option: Require mandatory NAT444.


## Block 2: Switching (module 2)

@quiz: What is the main purpose of VLANs on a switch?
@option: Automatically encrypt WAN traffic.
@correct: Logically segment broadcast domains on shared hardware.
@option: Replace IP addressing entirely.
@option: Increase Layer 3 MTU only.

@quiz: STP (802.1D) primarily aims to:
@option: Maximize uncontrolled Layer 2 loops.
@correct: Prevent bridging loops by computing a tree that blocks redundant ports.
@option: Translate IPv4 to IPv6.
@option: Load-balance BGP routes.

@quiz: EtherChannel bundles links to:
@option: Shrink MTU only.
@correct: Increase aggregate bandwidth and redundancy between switches.
@option: Remove VLAN tagging needs.
@option: Force half-duplex.

@quiz: A MAC flooding attack tries to:
@option: Encrypt STP BPDUs.
@correct: Saturate the CAM table to provoke hub-like flooding or instability.
@option: Rewrite OSPF prefixes.
@option: Disable legitimate WAN ARP.

@quiz: A switch trunk port typically carries:
@option: Exactly one untagged VLAN always.
@correct: Multiple VLANs using tagging (e.g., 802.1Q).
@option: IPv6-only traffic.
@option: Frames without FCS.

@quiz: Switch port security can limit:
@option: Only WAN bandwidth caps.
@correct: How many or which MAC addresses may appear on an access port.
@option: BGP route table size.
@option: Global DSCP priority.


## Block 3: Routing (module 3)

@quiz: A default IPv4 static route 0.0.0.0/0 acts as:
@option: Only an OSPF summary.
@correct: Gateway of last resort for non-matching destinations.
@option: An implicit ACL.
@option: A mandatory multicast address.

@quiz: OSPF is primarily a routing protocol that uses:
@option: Classic pure distance-vector updates only.
@correct: Link-state advertisements inside an AS.
@option: Exclusively inter-domain policy.
@option: Layer 2 STP control frames.

@quiz: An FHRP such as HSRP/VRRP provides:
@option: Mandatory IPsec encryption.
@correct: A redundant virtual default gateway if the active router fails.
@option: Exclusive NAT overload.
@option: A DNS replacement.

@quiz: Overloaded PAT/NAT allows:
@option: Unlimited stateless connections with no table.
@correct: Multiplexing many internal flows behind few public addresses using ports.
@option: Eliminating routing tables.
@option: Only translating IPv6 to MAC.

@quiz: Routers pick the RIB entry with the:
@option: Always /0 mask.
@correct: Longest matching prefix.
@option: Highest administrative distance as preferred.
@option: Even first octet only.

@quiz: A lower administrative distance in Cisco-like IOS implies:
@option: Less trustworthy routes.
@correct: Higher preference versus other sources for the same prefix.
@option: Static routes are forbidden.
@option: Automatic tie with external BGP.


## Block 4: Policy, QoS, automation (module 4)

@quiz: An extended IPv4 ACL typically filters on:
@option: Only BGP AS numbers.
@correct: Addresses, ports, and protocols per rule.
@option: Native VLAN only.
@option: Dynamic DNS names exclusively.

@quiz: An authoritative DNS server for `example.com`:
@option: Only performs recursive caching for stub clients.
@correct: Holds the zone records and answers authoritatively for that domain.
@option: Always replaces recursive resolvers.
@option: Operates strictly at Layer 2.

@quiz: QoS on IP networks often marks traffic using:
@option: Only MAC OUIs.
@correct: DSCP / CoS at different hops.
@option: VLAN 0 only.
@option: TTL exclusively.

@quiz: SDN conceptually separates:
@option: Layers 1 and 2 only.
@correct: Control plane from data plane (centralized decision making).
@option: IPv4 from IPv6 on hosts.
@option: Switching from physical cabling identically.

@quiz: Automating network configuration (Ansible, etc.) mainly targets:
@option: Removing OSI models from design.
@correct: Idempotency, repeatability, and fewer human errors.
@option: Replacing IP addressing.
@option: Banning SNMP always.

@quiz: ICMP is commonly used for:
@option: Bulk application payload transport.
@correct: Control messages such as unreachable or echo request/reply (ping).
@option: Replacing TCP for HTTP.
@option: Encrypting TLS sessions.
