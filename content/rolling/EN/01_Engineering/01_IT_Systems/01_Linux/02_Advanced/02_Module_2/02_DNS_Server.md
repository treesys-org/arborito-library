@title: DNS Server: BIND9 Configuration
@icon: 🌐
@description: Stop relying only on public resolvers. Run your own authoritative name server for internal networks: zones, A records, CNAME, and forwarders.
@order: 2

# BIND 9: forward zones, reverse zones, and recursion

An **authoritative** server answers for domains you manage; a **recursive resolver** (or **forwarder**) forwards everything else. **BIND** remains the reference in **LPIC-2** docs and many enterprises.

@section: 1. Core concepts

* **Zone:** a slice of the DNS tree you are responsible for.
* **Records:** **A/AAAA**, **CNAME**, **MX**, **NS**, **SRV**, **TXT** (SPF).
* **SOA:** serial, refresh/retry/expire; **increment the serial** on every change (often `YYYYMMDDnn`).
* **Reverse (PTR):** IP → name; IPv4 often under `in-addr.arpa`.

@section: 2. Install and layout (Debian)

```bash
sudo apt install bind9 bind9utils
```

Typical files:

* `/etc/bind/named.conf.options` — global options, forwarders, `allow-query`.
* `/etc/bind/named.conf.local` — site zones.
* `/etc/bind/db.*` — zone data.

**RHEL:** package `bind`, paths `/etc/named.conf`, `/var/named/`.

@section: 3. Forward zone (example)

`/etc/bind/named.conf.local`:

```text
zone "company.local" {
    type master;
    file "/etc/bind/db.company.local";
};
```

`/etc/bind/db.company.local` (adjust SOA and serial):

```text
$TTL 604800
@   IN SOA ns1.company.local. admin.company.local. (
        2026032901 ; Serial
        604800     ; Refresh
        86400      ; Retry
        2419200    ; Expire
        604800 )   ; Negative TTL
;
    IN NS     ns1.company.local.
ns1 IN A      192.168.1.10
www IN CNAME  ns1
```

@section: 4. Reverse zone (lab)

For `192.168.1.0/24`, the reverse zone is `1.168.192.in-addr.arpa`:

```text
zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.1";
};
```

```text
10    IN PTR ns1.company.local.
```

Public PTR for provider IPs is often managed by the ISP; on a LAN you control it.

@section: 5. Forwarders and recursion

In `named.conf.options`:

```text
options {
    directory "/var/cache/bind";
    forwarders { 1.1.1.1; 8.8.8.8; };
    forward only;
    dnssec-validation auto;
};
```

Do not expose **open recursion** to the Internet — **DNS amplification** risk. Restrict `allow-query` to your LAN or use **views**.

@section: 6. DNSSEC (overview)

Cryptographic signatures for answer integrity. BIND can **sign** zones (`dnssec-signzone`) or use **inline signing**. Conceptually: **KSK/ZSK** and **chain of trust**.

@section: 7. Diagnostics

```bash
sudo named-checkconf
sudo named-checkzone company.local /etc/bind/db.company.local
sudo systemctl reload bind9
dig @192.168.1.10 www.company.local
dig -x 192.168.1.10 @192.168.1.10
```

@section: 8. Alternative: Unbound (recursive only)

**Unbound** fits **caching** at the edge without authoritative zones. **Authoritative vs recursive** is a common exam distinction.

@section: 9. Lab

1. Stand up an internal zone with a forwarder; compare `dig +trace` externally vs internally.
2. Change a serial incorrectly and observe that secondaries (if any) do not update.

@quiz: Which SOA field must you increase when you edit zone records?
@option: TTL only
@correct: Serial
@option: Expire

@quiz: Which record type is typically used for a name alias (www → canonical host)?
@option: MX
@correct: CNAME
@option: TXT

@quiz: Which command validates the syntax of a BIND zone file?
@option: named-checkconf only
@correct: named-checkzone
@option: dig -x
