@title: Trust Relationships
@icon: 🤝
@description: How separate domains and forests authenticate to each other.
@order: 4

# Trust relationships

**Trusts** let principals in one domain access resources in another. **Intra-forest** trusts are automatic and transitive. **Inter-forest** trusts are explicit.

@section: Direction

If **Domain A trusts Domain B**, users from B can authenticate to resources in A (given permissions).

@section: Forest trust

Connects **entire forests** with optional **selective authentication** to reduce exposure.

@section: External trust

Between two domains **without** transitivity to the rest of a forest—common in migration scenarios.

@section: DNS and firewalls

Plan **conditional forwarders** / **stub zones** and allow Kerberos/LDAP/RPC ports per Microsoft guidance.

@quiz: Which trust type connects two Active Directory forests?
@option: External trust only
@correct: Forest trust
@option: Shortcut trust inside one tree only

@quiz: Which authentication protocol do Windows domain trusts primarily rely on?
@option: NTLM only
@correct: Kerberos
@option: Basic over HTTP
