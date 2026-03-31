@title: DNS in Windows: Zones and AD Integration
@icon: 🌐
@description: Name resolution as the foundation of Active Directory.
@order: 1

# DNS zones and AD integration

**DNS** locates domain controllers via **SRV records**. **AD-integrated zones** replicate with AD and support secure dynamic updates.

@section: Zone types

*   **Primary authoritative** for your domain.
*   **AD-integrated** replication (recommended for AD DNS zones).

@section: Common records

**A/AAAA**, **CNAME**, **MX**, **SRV** (`_ldap._tcp.dc._msdcs...`).

@section: Forwarders

Internal DNS resolves corporate names; **forwarders** resolve Internet names (verify DNSSEC policies if your org requires validation).

@section: Troubleshooting

```powershell
nslookup dc01.contoso.com
dcdiag /test:dns
```

@quiz: Which record type helps clients locate LDAP/Kerberos services for domain controllers?
@option: MX
@correct: SRV (with A records for targets)
@option: TXT only

@quiz: What is a key benefit of AD-integrated DNS zones?
@option: They are faster than BIND
@correct: They replicate with AD and support multimaster updates on DCs
@option: They remove the need for A records
