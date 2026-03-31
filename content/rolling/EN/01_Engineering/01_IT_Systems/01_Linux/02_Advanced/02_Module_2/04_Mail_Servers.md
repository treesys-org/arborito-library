@title: Mail Servers: Postfix and Dovecot Basics
@icon: 📧
@description: Introduction to the complex world of email. MTA vs MDA, basic Postfix configuration, and why outbound mail is hard today.
@order: 4

# Email on Linux: Postfix, Dovecot, and deliverability

Running your own mail is **hard** not because of packages, but because of **reputation**, **DNS**, and **abuse**. This lesson gives you the **LPIC-2** and interview model: MTA, MDA, SMTP authentication, and SPF/DKIM/DMARC.

@section: 1. Architecture: MTA, MDA, MUA

1. **MUA (Mail User Agent):** user client (Thunderbird, mutt).
2. **MTA (Mail Transfer Agent):** moves mail between servers via **SMTP** (port 25 server-to-server; 587/465 with auth for clients). **Postfix** is the usual Linux MTA.
3. **MDA / mailbox:** local delivery in **Maildir** or **mbox**. **Dovecot** provides **IMAPS/POP3S** for the MUA to read mail.

Typical flow: MUA → **submission** to Postfix (587) → Postfix delivers to another MTA (25) or to Dovecot via **LMTP**.

@section: 2. Postfix: parameters you cannot skip

Install (Debian/Ubuntu): `sudo apt install postfix`. The wizard asks for site type; in a lab use **Internet Site** with a consistent FQDN.

**`/etc/postfix/main.cf` (concepts):**

* **`myhostname`:** public FQDN (`mail.company.com`).
* **`mydestination`:** domains for which this host **terminates** mail locally.
* **`mynetworks`:** networks allowed to **relay** without authentication (trusted LAN only). Never **`0.0.0.0/0`** — that is an **open relay**.
* **TLS:** `smtpd_tls_cert_file`, `smtpd_tls_key_file`, or Let’s Encrypt paths.
* **SASL:** authenticated submission (Dovecot as SASL backend is a common pattern).

**Queues and troubleshooting:**

```bash
sudo postfix check
mailq
sudo postqueue -f
sudo tail -f /var/log/mail.log
```

@section: 3. Dovecot: IMAP and mailboxes

```bash
sudo apt install dovecot-imapd dovecot-lmtpd
```

* **Maildir:** `~/Maildir` with `cur`, `new`, `tmp`.
* Postfix must hand mail to Dovecot: **LMTP** (`virtual_transport = lmtp:unix:private/dovecot-lmtp`) depending on design.

**Local test:**

```bash
echo "test" | mail -s "test" user@localhost
```

@section: 4. Modern deliverability (SPF, DKIM, DMARC)

Without these, Gmail/Outlook **reject** or spam-folder mail.

* **SPF (TXT):** which hosts may send for your domain.
* **DKIM:** per-message cryptographic signature; public key in DNS.
* **DMARC:** policy (`none`, `quarantine`, `reject`) when SPF/DKIM alignment fails.
* **PTR (rDNS):** the server IP should resolve to a name consistent with `myhostname`.

**Tools:** `dig TXT company.com`, `opendkim-testkey`, online header checkers.

@section: 5. RHEL vs Debian

* **RHEL/Fedora:** `postfix`, `dovecot`; SELinux booleans (`postfix_*`, `dovecot_*`); **firewalld** for 25/587/993.
* **Debian/Ubuntu:** same daemons; paths `/etc/postfix`, `/etc/dovecot`; **ufw** equivalent.

@section: 5b. Relay, queues, and anti-abuse

**Open relay** (accepting mail from the Internet for arbitrary destinations) turns you into a **spam relay** in minutes. Postfix mitigates this with:

* **`smtpd_recipient_restrictions`** / **`smtpd_relay_restrictions`** — order matters; typically `permit_mynetworks`, `permit_sasl_authenticated`, `reject_unauth_destination`.
* **Greylisting** and **DNSBLs** are optional extra layers.

**Stuck queues:** `mailq` shows IDs; `postsuper -d ALL` wipes the queue in emergencies (you **lose mail**); `postcat -q <id>` inspects one message. If the disk is full, Postfix may stop accepting—check `df` and `/var/spool/postfix`.

**Headers:** `Received:`, `Authentication-Results:`, `DKIM-Signature` — learn to read them to see whether failure is **before** or **after** your server.

@section: 6. Guided lab

1. Install Postfix and Dovecot on test VMs.
2. Configure **local delivery only** and verify with `swaks` or `telnet` to port 25 from another VM.
3. Add test SPF/DKIM records in lab DNS and verify with `dig`.
4. Simulate a stuck queue: break outbound DNS or fill `/var` and watch `mailq` and logs change.

@section: 7. Realistic expectations (2026)

Reliable **outbound** mail to Gmail/Outlook needs a clean IP, PTR, aligned SPF/DKIM/DMARC, and sometimes **IP warmup**. Many orgs delegate outbound relay to **SendGrid**, **Amazon SES**, **Mailgun**, etc., and only run **inbound** internally. That is not failure—it is **separation of concerns**.

@quiz: What risk does an overly broad `mynetworks` in Postfix create?
@option: Weak TLS
@correct: Open relay: others send spam through your server
@option: Slow IMAP

@quiz: Which DNS record indicates which hosts may send mail for a domain?
@option: MX
@correct: SPF (TXT record)
@option: SRV

@quiz: Which protocol does Thunderbird normally use to read mail from the server?
@option: SMTP port 25
@correct: IMAPS (143/993) or POP3S
@option: LDAP
