@title: SSL/TLS Certificate Management (OpenSSL)
@icon: 📜
@description: Encryption for everyone. Understand CSR vs CRT vs key. Create self-signed certs and use Certbot for free HTTPS.
@order: 3

# TLS on Linux: OpenSSL, private PKI, and Let’s Encrypt

**X.509** certificates underpin **HTTPS**, **LDAPS**, **SMTPS**, and **IMAPS**. For **LPIC-2** and interviews you must explain: private key, chain of trust, CSR, and automated renewal.

@section: 1. Pieces of a TLS deployment

* **Private key (`.key`):** server secret; permissions `0600`, owned by root or the service account.
* **Public certificate (`.crt` / `.pem`):** public key + metadata + CA signature.
* **Chain / intermediate:** links your cert to a **root CA** browsers trust.
* **CSR (Certificate Signing Request):** sent to a public or internal CA; **does not** contain the private key.

@section: 2. OpenSSL survival commands

**Self-signed (lab):**

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout server.key -out server.crt -subj "/CN=lab.local"
```

**Generate CSR for a corporate CA:**

```bash
openssl req -new -newkey rsa:2048 -nodes \
  -keyout domain.key -out domain.csr
```

**Inspect certificate:**

```bash
openssl x509 -in server.crt -text -noout
```

**Verify chain against CA file:**

```bash
openssl verify -CAfile ca-chain.pem server.crt
```

@section: 3. Let’s Encrypt and Certbot

Public CA with **~90 day** validity — **automatic renewal** is mandatory.

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d www.example.com
```

**Renewal:** `certbot renew` (systemd timer or cron). Test with `certbot renew --dry-run`.

**HTTP-01 challenge:** Certbot places a token under the webroot; port 80 must be reachable (or use **DNS-01** with your provider’s plugin).

**DNS-01** is required when:

* The site has no port 80 open.
* You need certs for **internal names** or **wildcards** (`*.example.com`).
* The load balancer terminates TLS and the origin never sees the HTTP challenge.

Requires your DNS provider’s API (credentials under `/etc/letsencrypt/` with strict permissions).

**Modern TLS:** minimum **1.2** (prefer **1.3**), strong cipher suites, **OCSP stapling** on Nginx/Apache. `openssl s_client -connect host:443 -tls1_2` tests compatibility.

@section: 4. Internal PKI

Many orgs run an **internal CA** (OpenSSL, **cfssl**, **step-ca**). You must **distribute** the root certificate to trust stores (OS/browser or MDM profiles).

**openssl ca** or dedicated tools handle revocation (**CRL**, **OCSP**).

@section: 5. RHEL vs Debian paths

* Typical paths: `/etc/pki/tls` (RHEL), `/etc/ssl` (Debian).
* **certmonger** / **getcert** on RHEL can renew IPA/FreeIPA certificates.
* SELinux: correct contexts on files under `/etc/nginx` or `/etc/httpd`.

@section: 6. Common mistakes

* Incomplete chain (missing intermediate) → browser warnings.
* Expired certificate → monitor with **Icinga**, **Prometheus blackbox**, etc.
* World-readable private key → audit failure.

@section: 7. Extended lab

1. Create a self-signed cert and configure **nginx** or **apache** in a VM.
2. Run `openssl s_client -connect localhost:443 -servername lab.local` and review the handshake (chain, TLS version, signature algorithm).
3. Set up **Certbot** on a test domain or use `--staging` to avoid rate limits; check the systemd **timer**: `systemctl list-timers | grep certbot`.
4. Document what you would do if a production certificate expires: emergency rotation, API client communication, and **HSTS** risk (do not strand users with the wrong cert).

@section: 8. Pre-production checklist

* Full chain (leaf + intermediate) served by the TLS terminator.
* Private key permissions correct; service runs as an unprivileged user where possible.
* Renew before 30 days to expiry; monitoring alerts.
* Test `openssl verify` and browser access from an external network (not only localhost).

@quiz: Which file must never be exposed publicly?
@option: CSR
@option: .crt certificate
@correct: Private key (.key)
@option: Intermediate chain

@quiz: Which Let’s Encrypt client often auto-edits Nginx configuration?
@option: openssl ca
@correct: certbot with the --nginx plugin
@option: ssh-copy-id

@quiz: Why does Let’s Encrypt require automated renewal?
@option: TLS 1.2 is forbidden
@correct: Certificates are short-lived (~90 days)
@option: It only works on Windows
