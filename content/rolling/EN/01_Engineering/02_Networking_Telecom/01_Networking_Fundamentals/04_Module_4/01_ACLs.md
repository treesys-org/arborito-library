@title: ACLs: Filtering on Routers and Firewalls
@icon: 🧱
@description: Standard vs extended, evaluation order, objects, reflexive ACLs.
@order: 1

# Access lists: explicit policy at layers 3/4

**ACLs** filter traffic by **source/destination IP**, **protocol**, **ports**, sometimes **TCP flags**. Cisco routers have **standard (1–99)** and **extended** ACLs; modern firewalls use **objects** and ordered rules. This lesson covers **order**, **implicit deny**, **logging**, and **reflexive** ACLs.

@section: Evaluation order

Rules are evaluated **top-down**; first match wins. **Implicit deny** drops anything not permitted.

**Counters** help find unused or mis-hit rules.

@section: Typical extended ACL

Allow `10.0.0.0/24` to `tcp/443` toward `198.51.100.0/24`, deny the rest. **Established** on TCP for return traffic (careful with stateless vs stateful designs).

**Object-groups** reduce repetition.

@section: Reflexive ACLs

In stateless router-only environments, **reflexive** ACLs allow return traffic tied to outbound-initiated sessions.

**Stateful** firewalls replace this with connection tables.

@section: IPv6 ACLs

Parallel syntax; remember required **ICMPv6** allowances for NDP (tight rules).

@section: Common mistakes

* Overly wide permits (`any any`) “temporarily” that never get tightened.
* Forgetting **return paths** with asymmetric routing.

@section: Suggested lab

1. Create an extended ACL on a lab router and verify with `show access-lists`.
2. Generate permitted and denied traffic; watch hit counters.
3. Translate the same policy to `nftables` syntax on Linux for comparison.

@quiz: What happens if no ACL line matches an IPv4 packet?
@option: It is permitted by default
@correct: It is implicitly denied
@option: It is sent to the switch CPU
