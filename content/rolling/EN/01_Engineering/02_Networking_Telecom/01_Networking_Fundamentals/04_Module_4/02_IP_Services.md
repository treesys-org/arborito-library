@title: IP Services: DHCP, DNS, and NTP
@icon: 🌐
@description: Dynamic addressing, name resolution, and time synchronization.
@order: 2

# Core services: DHCP, DNS, and NTP

Without **DHCP**, hosts may not get IP addresses; without **DNS**, apps cannot resolve names; without **NTP**, logs and TLS certificates drift. This lesson ties **scopes**, **options** (router option 3, DNS option 6), **recursive vs authoritative DNS**, and **NTP strata**.

@section: DHCP

**DORA** (Discover/Offer/Request/ACK). **Relay** (`ip helper-address`) forwards broadcasts between VLANs to a central server.

**Reservations** by MAC for printers/services; **pools** per VLAN.

**Snooping** (layer-2 lesson) blocks rogue servers.

@section: DNS

* **Authoritative** for your `example.com` zone.
* **Recursive** for internal clients (resolver).

**Split-horizon DNS** serves different internal/external views. **DNSSEC** signs records; validation at resolvers.

**TTL** affects change agility and cache load.

@section: NTP

**Stratum** measures distance to a reference clock. **Authentication** between servers in critical environments.

**Leap seconds** and **smearing** on cloud providers: read docs for distributed systems.

@section: Common mistakes

* DHCP missing **default gateway** option.
* Internal DNS without **PTR** records for mail troubleshooting (SPF/DMARC side effects).

@section: Suggested lab

1. Run `dnsmasq` or BIND on a VM and create an internal `lab.local` zone.
2. Configure DHCP relay on an L3 router toward a central server simulation.
3. Sync hosts with **chrony**/`ntpd` and verify with `ntpstat`.

@quiz: What is a DHCP relay agent on an L3 router mainly for?
@option: Encrypting DHCP
@correct: Forwarding DHCP broadcasts between subnets to a central server
@option: Auto-assigning VLANs without 802.1Q
