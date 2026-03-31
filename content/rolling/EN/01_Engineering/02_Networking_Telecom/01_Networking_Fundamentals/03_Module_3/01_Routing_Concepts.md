@title: Routing Concepts: Tables, Longest Match, and Metrics
@icon: 🧭
@description: Next hop, AD, connected vs dynamic protocols, route summarization.
@order: 1

# IP routing: how a router picks the next hop

**Routing** decides **where** to forward non-local IP packets. Decisions use the **routing table**, **longest prefix match**, **administrative distance**, and dynamic protocol **metrics**. This lesson separates **default routes**, **host routes**, and **null0**.

@section: Longest prefix match

If both `10.0.0.0/8` and `10.1.0.0/16` exist, traffic to `10.1.0.5` picks **/16** as more specific.

The **default route** `0.0.0.0/0` is last resort.

@section: Administrative distance (AD)

When **two sources** advertise the same route, lower **AD** wins (static vs OSPF vs eBGP per vendor). **Metric** decides within the same protocol.

**Floating static** uses higher AD for backup.

@section: Connected and static

**Directly connected** after configuring interface IPs; **static** for fixed paths. **Recursive next-hop** if pointing to an IP without explicit outgoing interface.

**Null routes** drop traffic (blackhole) for mitigation or aggregation.

@section: Load balancing and ECMP

**ECMP** spreads flows across equal-cost next hops; may be **per-packet** or **per-hash** depending on implementation.

@section: Common mistakes

* **Routing loops** from poorly designed bidirectional statics.
* **Asymmetric paths** breaking **stateful firewalls** without care.

@section: Suggested lab

1. Create three routes to the same destination with different prefix lengths and observe which wins.
2. Configure a floating static with higher AD and fail the primary.
3. Use `traceroute` to verify paths.

@quiz: What rule does a router use to choose between two matching routes?
@option: Highest metric
@correct: Longest prefix match (most specific prefix)
@option: First alphabetical entry
