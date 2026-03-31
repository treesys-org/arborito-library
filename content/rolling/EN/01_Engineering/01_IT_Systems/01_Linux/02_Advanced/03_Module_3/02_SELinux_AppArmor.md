@title: SELinux & AppArmor: Mandatory Access Control
@icon: 👮
@description: Security beyond rwx. Confine processes so that if they are compromised, damage stays limited.
@order: 2

# MAC: SELinux and AppArmor in depth

Traditional UNIX permissions are **DAC** (Discretionary Access Control): the owner decides. **MAC** (Mandatory Access Control) adds system-wide policies that **even root** cannot bypass when the policy forbids it (depending on mode). **LPIC-2** and **RHCE** treat SELinux as core material.

@section: 1. AppArmor (Ubuntu / Debian / SUSE)

Path-based model: profiles live under `/etc/apparmor.d/`.

**Modes:**

* **enforce** — blocks violations.
* **complain** — logs only (useful when building profiles).

```bash
sudo aa-status
sudo aa-enforce /usr/sbin/nginx
sudo aa-complain /usr/bin/mysqld
```

**Logs:** `dmesg`, `journalctl`, sometimes `/var/log/audit/` when auditd integrates messages.

**New profile:** `aa-genprof command` (learning mode in a lab).

**Profiles and abstractions:** `/etc/apparmor.d/abstractions/` holds reusable pieces (network, TLS, etc.). Including abstractions reduces noise and keeps policies readable. **`aa-logprof`** suggests rules from recent denials.

@section: 2. SELinux (RHEL / Fedora / CentOS)

Label-based: user, role, type, optional MLS/MCS level.

**Modes (`/etc/selinux/config`):**

* **enforcing** — policy enforced.
* **permissive** — logs only (troubleshooting).
* **disabled** — requires reboot to re-enable.

```bash
getenforce
sudo setenforce 0   # temporary permissive
```

**Process and file contexts:**

```bash
ps auxZ
ls -Z /var/www/html
```

`cp` may inherit wrong context; **`restorecon -Rv path`** reapplies defaults.

**Booleans** (policy toggles):

```bash
getsebool -a | grep httpd
sudo setsebool -P httpd_can_network_connect 1
```

**Ports:** SELinux knows which ports each domain may bind (`semanage port -l`).

**Persistent contexts:** `chcon` changes live but may be lost on policy restore; in production use **`semanage fcontext -a`** + **`restorecon`**. Example: add `/srv/sites(/.*)?` with type `httpd_sys_content_t`.

**`dontaudit`:** rules that hide expected denials; if something “fails silently”, try temporarily `semodule -DB` to disable dontaudits (diagnostics only—revert afterward).

@section: 3. Troubleshooting tools

* **`audit2allow`** — builds policy modules from denials (use carefully).
* **`sealert` / setroubleshoot** — human-readable messages in logs.
* **Rule of thumb:** if it works in permissive but not enforcing, **do not** disable SELinux permanently; fix context or booleans.

@section: 4. Quick comparison

| Topic | AppArmor | SELinux |
| :--- | :--- | :--- |
| Model | Paths, profiles | Labels, policies |
| Learning curve | Often gentler | More powerful, more complex |
| RH exams | Less common | Very common |

@section: 5. Lab

1. Create a file under `/root`, copy it to `/var/www/html`, try to serve it with httpd. Inspect `ls -Z` and fix with `restorecon`.
2. List `httpd`-related booleans and document what each enables.
3. After a failure, use `ausearch -m avc -ts recent` and correlate with `sealert` if installed.
4. Explain in your own words why leaving SELinux **permissive** forever is a bad idea on exposed servers.

@quiz: Which command reapplies default SELinux contexts on a directory tree?
@option: chcon -R
@correct: restorecon -Rv
@option: chmod -R

@quiz: In AppArmor, which mode only logs violations without blocking?
@option: enforce
@correct: complain
@option: disabled

@quiz: What does `ls -Z` show beyond `ls -l`?
@option: File size only
@correct: MAC security contexts (SELinux)
@option: inode number
