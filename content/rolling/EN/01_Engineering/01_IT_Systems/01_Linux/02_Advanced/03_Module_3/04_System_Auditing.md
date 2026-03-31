@title: System Auditing: auditd and forensic logs
@icon: 🕵️
@description: Who deleted that file? Who changed the clock? Configure auditd to trace syscalls and build an authoritative trail.
@order: 4

# Linux Audit (`auditd`): traceability and compliance

Application logs (`/var/log/auth.log`) describe **high-level events**. **auditd** intercepts **kernel syscalls** and answers audit questions: “Who ran `chmod` on this binary?” or “Who read `/etc/shadow`?”

@section: 1. Architecture

* **auditd** loads rules from `/etc/audit/audit.rules` (or fragments in `/etc/audit/rules.d/`).
* Events land in `/var/log/audit/audit.log` (verbose format).
* Tools: **`auditctl`** (live rules), **`ausearch`** (query), **`aureport`** (summaries).

**RHEL:** `auditd` service; **Debian/Ubuntu:** same stack; minimal containers may omit it — verify.

@section: 2. Rules: files, syscalls, executables

**Watch a file:**

```bash
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
```

* `-p wa`: write + attribute change.
* `-k`: search key.

**Watch syscalls (e.g. `execve`):** advanced rules in `audit.rules` for sensitive binaries.

**Persistence:** rules under `/etc/audit/rules.d/*.rules` then `augenrules --load` or restart `auditd`.

@section: 3. Forensic queries

```bash
sudo ausearch -k passwd_changes
sudo ausearch -ui 1000 -ts today
sudo aureport --auth --summary
```

**Useful fields:** `auid` (login UID), `uid` (effective), `exe`, `success=yes|no`.

@section: 4. Integration with systemd / journal

`journalctl` may show related messages; for **chain of custody**, many orgs **forward** audit to a SIEM. `auditd` remains the **authoritative** syscall source.

@section: 5. Performance and risk

Too many rules → **I/O and CPU** overhead. Poor rules create **noise**. Test in staging; use consistent **keys**.

@section: 6. Quick comparison: auditd vs syslog

| Aspect | auditd | Traditional syslog |
| :--- | :--- | :--- |
| Granularity | syscalls, inodes | application messages |
| Detail | very high | depends on daemon |
| Typical use | PCI-DSS, investigations | day-to-day ops |

@section: 7. Advanced rules: syscalls and lists

Besides `-w` on files, you can audit specific **syscalls**. Typical pattern in `audit.rules`:

```text
-a always,exit -F arch=b64 -S chmod -S fchmod -S fchmodat -F auid>=1000 -F auid!=4294967295 -k critical_perms
```

* `-a always,exit`: log on **syscall exit** (you see success/failure).
* `-F arch=b64`: architecture (on 64-bit systems; sometimes `b32` too).
* `-S`: syscall list (`unlink`, `rename`, `execve` — see `ausyscall --dump`).
* `-F auid>=1000`: focus on “human” users (not only daemons with auid `-1` or `4294967295` = *unset*).

**Exclusion lists:** in noisy environments combine inclusion rules with `exclude` or tighten filters and use **keys** for SIEM correlation.

@section: 8. `auditd.conf`: space, retention, performance

`/etc/audit/auditd.conf` controls rotation (`max_log_file`, `max_log_file_action = ROTATE`), retention, and **backlogs**. If the kernel drops events you may see backlog warnings. On very busy systems:

* Increase the kernel **buffer** (`-b` in rules — carefully).
* Remove redundant rules.
* Forwarding to syslog via `dispatcher` duplicates I/O—only if your SIEM consumes it that way.

@section: 9. Chain of custody and compliance

For **PCI-DSS**, **ISO 27001**, or internal audits you need: **who** did **what**, **when**, and logs the same admin cannot rewrite. Append-only `audit.log` (`chattr +a`) or immediate ship to a **remote log host** with signing is common. Compare with **journald** forwarding: useful, but it does not replace syscall granularity from `auditd` when required.

@section: 10. Extended lab

1. Add a temporary watch on `/etc/hosts`, make a change, search with `ausearch -k` and note `auid`, `uid`, `comm`.
2. Run `aureport --failed` after several failed `su` attempts and correlate with `ausearch -m USER_AUTH`.
3. List syscalls with `ausyscall x64 chmod` and sketch (on paper or a VM) a rule auditing deletes in a project directory.
4. Check `/var/log/audit/` size and rotation policy in `auditd.conf`.

@quiz: Which tool queries auditd’s binary log filtering by `-k` key?
@option: grep /var/log/syslog
@correct: ausearch
@option: journalctl --audit

@quiz: What does `auditctl -w` use to define read/write/attribute permissions?
@option: -k
@correct: -p (rwxa)
@option: -f

@quiz: Why not enable blanket global syscall rules in production without filters?
@option: Saves disk space
@correct: Massive event volume degrades performance
@option: Technically impossible
