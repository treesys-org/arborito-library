@title: Static Routing: Simplicity and Operational Risk
@icon: 📍
@description: Static routes, recursive routes, floating statics, troubleshooting.
@order: 2

# Static routing: full control, manual maintenance

**Static routes** are great for **stub networks**, **defaults** on hosts, or simple **aggregates**. They **do not scale** with frequent changes and can create **black holes** without **next-hop reachability**. This lesson covers **next-hop**, **outgoing interface**, **floating statics**, and verification.

@section: Next-hop vs outgoing interface

**Next-hop IP:** the router resolves L2 toward that IP. **Outgoing interface** on point-to-point links may be valid but less explicit on multi-access.

**Proxy ARP** can hide mistakes; prefer explicit routes.

@section: Floating routes

Same destination with **higher AD** acts as backup if the primary disappears.

**Track** IP SLA (Cisco) or **BFD** elsewhere to withdraw an active route.

@section: Hosts

Endpoints use **static default gateway** or DHCP. Multi-homed server static routes need care with **asymmetry**.

@section: Troubleshooting

* Ping the next-hop.
* `show ip route` / `ip route` on Linux.
* Recursive lookup fails if there is no route to the next-hop.

@section: Common mistakes

* Static toward a remote network without symmetric return path.
* **Overlapping** statics and dynamics without policy.

@section: Suggested lab

1. Configure two routers with reciprocal static routes and validate end-to-end ping.
2. Add a floating route and simulate primary link failure.
3. Document a route matrix for a fictional site.

@quiz: When is a floating static route typically preferred?
@option: Always replacing OSPF
@correct: As backup when the lower-AD primary route disappears
@option: To increase BGP metrics
