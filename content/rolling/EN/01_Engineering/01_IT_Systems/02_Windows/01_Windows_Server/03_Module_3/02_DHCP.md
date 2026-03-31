@title: DHCP: Scopes, Reservations, and Failover
@icon: 🔢
@description: Assigning IP configuration automatically.
@order: 2

# DHCP scopes, reservations, and failover

**DHCP** hands out IPv4/IPv6 addresses plus options (router, DNS, domain suffix). Plan **scopes**, **exclusions**, **reservations**, and **failover** pairs for availability.

@section: Core objects

*   **Scope / range** of assignable addresses.
*   **Exclusion** for static servers inside the range.
*   **Reservation** maps MAC → fixed IP.

@section: Critical options

*   **003 Router** — default gateway.
*   **006 DNS servers** — usually internal DNS/DCs, not random ISP routers.
*   **015 DNS domain name** — suffix for short name resolution.

@section: Authorization

Authorize DHCP servers in AD to prevent rogue DHCP on enterprise LANs.

@section: Failover

**Hot standby** or **load balance** between two DHCP servers—plan partner relationships and split scopes if legacy.

@quiz: Which DHCP option sets the default gateway for clients?
@option: 006
@correct: 003 Router
@option: 015

@quiz: Why authorize a DHCP server in Active Directory?
@option: Make DHCP faster
@correct: Prevent unauthorized DHCP servers on the network
@option: Encrypt DHCP traffic by default
