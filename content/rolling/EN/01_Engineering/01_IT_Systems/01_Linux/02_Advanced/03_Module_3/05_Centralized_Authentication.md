@title: Centralized Authentication: LDAP and Kerberos
@icon: 🏛️
@description: Enterprise identity basics. Stop creating local users on every server — connect them to a central directory.
@order: 5

# Centralized identity: LDAP, Kerberos, and SSSD

Managing dozens or hundreds of servers with **local accounts** (`useradd` everywhere) is unmaintainable. Real environments use a **directory** (LDAP or Active Directory) and authentication protocols (Kerberos), coordinated on Linux by **SSSD**.

@section: 1. LDAP in one sentence

**LDAP** (Lightweight Directory Access Protocol) queries and updates a **tree** of entries (users, groups, machines, OUs). It is not a relational DB: it is optimized for **reads** and hierarchies.

* **OpenLDAP** is the most common free implementation in generic docs.
* **389 Directory Server**, **FreeIPA** / **Red Hat IdM** bundle LDAP + Kerberos + DNS + policies.
* **Microsoft Active Directory** also speaks LDAP; Linux hosts join via **SSSD** + **realmd**.

**DN (Distinguished Name):** unique path in the tree, e.g. `uid=ana,ou=people,dc=company,dc=local`.

@section: 2. Kerberos: tickets, not passwords every hop

**Kerberos** avoids sending the password on every service request. Simplified flow:

1. User authenticates to the **KDC** (Key Distribution Center) and gets a **TGT** (Ticket Granting Ticket).
2. For NFS, SSH with GSSAPI, HTTP with SPNEGO, the client presents derived tickets without sending the secret in clear text.

**Time sync:** tickets are short-lived; **bad NTP** is the #1 cause of “mysterious” Kerberos failures.

@section: 3. SSSD: glue on the Linux host

**SSSD** resolves users against LDAP/AD, obtains Kerberos tickets, and **caches** credentials so brief directory outages are tolerated.

Key files (distro-dependent):

* `/etc/sssd/sssd.conf` — domains, LDAP URIs, `krb5.conf` realm.
* **authselect** (RHEL) or **PAM** under `/etc/pam.d/sshd` chaining SSSD.

**`getent passwd user`** — after SSSD is configured, directory users should appear.

**`realm join`** — assisted AD join on modern RHEL/Fedora/Debian; verify firewall and DNS (SRV records).

@section: 4. FreeIPA / IdM (architecture view)

**FreeIPA** / **Red Hat Identity Management** provides:

* Integrated LDAP + Kerberos + DNS.
* **HBAC** and centralized **sudo** rules.
* Certificates and password policy.

**LPIC-2** expects you to **understand** each component’s role, not memorize every `ldapsearch` attribute.

@section: 5. `krb5.conf` and `sssd.conf` (conceptual)

**`/etc/krb5.conf`** defines the **realm** (Kerberos domain in capitals, e.g. `COMPANY.LOCAL`), **KDCs** (`kdc = ...`), and ticket lifetimes. Typos in the realm or KDC hostname produce opaque errors like *“Cannot find KDC for realm”*.

**`/etc/sssd/sssd.conf`** (simplified) has `[sssd]` with `domains`, and `[domain/COMPANY]` with:

* `id_provider = ldap` or `ad`
* `auth_provider = krb5` or `ad`
* `ldap_uri`, `ldap_search_base`, `krb5_server`, `krb5_realm`

**NSS/PAM:** `sss` must appear in `/etc/nsswitch.conf` (`passwd`, `group`, `shadow`) and in `/etc/pam.d/common-auth` or equivalents (RHEL uses **authselect**). Without that, `getent` never sees domain users.

@section: 6. Quick diagnostics

```bash
sudo systemctl status sssd
sudo journalctl -u sssd -e
ldapsearch -x -H ldap://server -b "dc=company,dc=local" "(uid=ana)"
klist
```

**Typical failures:** DNS not resolving KDC; clock skew; LDAP TLS cert rejected; firewall blocking 389/636/88.

**First-aid table:**

| Symptom | Check first |
| :--- | :--- |
| `kinit` fails but network works | Time (`chronyc sources` / `timedatectl`), realm, KDC DNS name |
| `getent passwd user` empty | `sssd` running, `nsswitch`, `ldap_search_base` |
| SSH login with AD user fails | PAM, `authselect`/SSSD, `journalctl -u sssd` |
| LDAP TLS error | CA in `/etc/ssl` or `ldap_tls_cacert`, clock |

@section: 7. Mental lab

1. List three advantages of central identity vs local accounts on 50 servers.
2. Why do Kerberos and LDAP often appear together in enterprises?
3. Sketch on paper: laptop → SSH → Linux server → `sssd` → LDAP/AD for **uid** and Kerberos for **tickets** (password not sent to every service).

@quiz: Which daemon typically integrates LDAP/Active Directory with PAM and NSS on Linux?
@option: httpd
@correct: sssd (System Security Services Daemon)
@option: cupsd

@quiz: What almost always breaks Kerberos tickets even when the password is correct?
@option: Full disk
@correct: Time skew between client and KDC (NTP)
@option: chmod 777 on /tmp

@quiz: Which command shows the current user’s Kerberos tickets?
@option: kinit
@correct: klist
@option: ticket-show
