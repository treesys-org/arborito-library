@title: System Logs: Linux Secret Diary (/var/log & journalctl)
@icon: 📋
@description: The definitive guide (+500 lines) to digital forensics. Learn to read system thoughts, diagnose realtime failures, master 'tail -f' and 'journalctl'.
@order: 4

# The Secret Diary: Reading Linux's Mind

Welcome to digital forensics.

Windows server fails, cryptic error (0x8004005) sad face "Something wrong". Dark, unknown.

Linux server fails, **screams exactly what hurts, why, where, when**.
Problem screams language newbies can't read: **Logs**.

Logs intimate OS diary.
*   Connect USB, Kernel writes line.
*   SSH guess password, auth writes line.
*   Program crash, system writes line.
*   Email sent, mail server writes line.

Admin no read logs doctor no X-ray guess disease. Lottery. Production lottery lose data hacked.

Massive guide, read diary. Two logging systems (Classic text, Modern binary), future realtime `tail -f`, past `journalctl`.

Prepare. Seek truth.

@section: 1. Two Logging Worlds

Linux transition decade. Two philosophies exist. Master both.

### 1.1 Classic (Syslog / Text Files)
Traditional Unix, 70s.
*   **Phil:** "Everything text file".
*   **Work:** Daemon (`rsyslog` `syslog-ng`) listens writes text `/var/log`.
*   **Pro:** Accessible. Read editor (`nano`, `cat`). System break, disk mount other PC read easy. Bulletproof.
*   **Con:** "Dumb". No index. Search 2GB `grep` regex. Rotate delete external.

### 1.2 Modern (Systemd Journal / Binary)
Standard modern (Ubuntu, Fedora, CentOS).
*   **Phil:** "Logs structured data".
*   **Work:** Binary DB optimized central `systemd-journald`.
*   **Pro:** Power. Indexed. Query "logs yesterday 4-5 error SSH". Instant. Tamper-proof.
*   **Con:** No `cat` `nano`. Weird symbols. Need `journalctl`.

**Hybrid Reality:**
Most Linux today **both**. Systemd collects, forwards classic text. Best both.

@section: 2. Paper Bunker: `/var/log`

Start classic, saves you when fail. Terminal heart darkness:

```bash
$ cd /var/log
$ ls -l
```

Files. Field guide critical. Memorize best friends.

### 2.1 Sacred Files

#### 1. `/var/log/syslog` (Debian/Ubuntu) or `/var/log/messages` (RHEL)
*   **Catch-all.**
*   General system info, kernel non-critical, service generic, cron.
*   **When:** "Something weird" unknown component. System weird note here.

#### 2. `/var/log/auth.log` (Debian/Ubuntu) or `/var/log/secure` (RHEL)
*   **Security Log.**
*   Vital. Auth authorization events.
*   **Contains:**
    *   Login attempts (SSH, local).
    *   `sudo` usage (who ran root).
    *   User group create delete.
    *   Pass change.
*   **When:** Suspect hack, forgot login, audit admin `sudo`.

#### 3. `/var/log/dmesg` or `/var/log/kern.log`
*   **Kernel Voice.**
*   `dmesg` command, content here.
*   **Contains:** Hardware Kernel. Disk detect, network init, USB, temp critical, RAM error, segfault.
*   **When:** USB no appear, net no up, PC reboot heat.

#### 4. `/var/log/apache2/` or `/var/log/nginx/` (App Subs)
*   Large apps (Web, DB, Backup) polite create subfolder `syslog` clean.
*   Standard:
    *   **`access.log`**: Good (visit, URL, 200).
    *   **`error.log`**: Bad (500, PHP fail, config broken).

### 2.2 Log Rotation (`logrotate`)
Look `/var/log`, weird names `syslog.1`, `syslog.2.gz`.
What?

Logs grow infinite. Busy server 1GB day. Fill disk collapse.
Linux **Logrotate** auto (cron night):

1.  **Rename:** `syslog` -> `syslog.1`.
2.  **Restart:** Create `syslog` new empty today.
3.  **Compress:** `syslog.1` yesterday gzip save space `syslog.2.gz`.
4.  **Clean:** Delete oldest (`syslog.52.gz`) free space.

**Lesson:** `/var/log/syslog` **RECENT** (morning now). Last week not there. Look compressed `.gz` (`zcat`, `zgrep`).

@section: 3. Reading Tools: Please no `cat`

Newbie error:
```bash
$ cat /var/log/syslog
```
**ERROR.**
500,000 lines. `cat`:
1.  Terminal flood Matrix.
2.  Can't read.
3.  Buffer saturated, history lost.
4.  See last 20 lines.

### 3.1 `less`: Civilized
Read static, `less`. Pager.

```bash
$ sudo less /var/log/syslog
```
*(Note: `sudo` needed private logs `auth.log`).*

**Nav `less`:**
*   **Arrows:** Line.
*   **Space:** Page down.
*   **b:** Page up.
*   **G**: End.
*   **g**: Start.
*   **/**: Search. `/error` Enter. `n` next.
*   **q**: Quit.

### 3.2 `tail`: Scalpel
Care 5 mins ago.
`tail` end.

```bash
# Last 10 lines
$ sudo tail /var/log/syslog

# Last 50
$ sudo tail -n 50 /var/log/syslog
```

@section: 4. Superpower: Realtime Monitor (`tail -f`)

Situation:
Config web server. Start fail.
Log, `less`, end, read. Exit. Fix. Start. Log `less` end...
Slow. Tedious.

Pros **watch log live movie**.

Magic:
```bash
$ sudo tail -f /var/log/syslog
```
*(`-f` **Follow**).*

**Work:**
1.  Shows last 10.
2.  **No close.** Waits attached cursor blink.
3.  System writes line, `tail` prints instant.

**Diag Exercise:**
1.  Term 1 `sudo tail -f /var/log/auth.log`. Corner.
2.  Term 2 SSH fail pass.
3.  Term 1 see error instant! "Failed password..."

Fundamental Troubleshooting. Correlate **action** **reaction** realtime.

Exit `Ctrl + C`.

@section: 5. Modern DB: `journalctl`

Future: **Systemd Journal**.
No files. No path. Query tool: **`journalctl`**.

Run `journalctl`, ALL logs ever. Hose. Trick **filter**.

### 5.1 Time
"Yesterday morning?"
Text files search `.gz` unzip time.
`journalctl` natural:

```bash
# Since yesterday
$ journalctl --since "yesterday"

# 1 hour
$ journalctl --since "1 hour ago"

# Interval
$ journalctl --since "2023-10-20 14:00:00" --until "2023-10-20 15:00:00"
```

### 5.2 Service (`-u`)
Used most. Nginx SSH. No kernel cron.
Systemd services "Units". `-u`.

```bash
# SSH
$ journalctl -u ssh

# Docker
$ journalctl -u docker

# Nginx today
$ journalctl -u nginx --since "today"
```

### 5.3 Boot (`-b`)
Reboot bad, see previous session crash.
Text logs mix. `journalctl` "Boot".

```bash
# Current boot
$ journalctl -b

# Previous boot (yesterday)
$ journalctl -b -1

# 2 boots ago
$ journalctl -b -2
```
Magic diag crash. `journalctl -b -1` end (`G`) last words "will".

### 5.4 Realtime (`-f`)
`tail`, `journalctl` follow.

```bash
# SSH realtime
$ journalctl -u ssh -f
```
Colored formatted. Readable.

### 5.5 Other
*   **Errors (`-p`):** Priority "Error" "Crit". Remove "Info". Quick broken.
    ```bash
    $ journalctl -p err
    ```
*   **Kernel (`-k`):** Kernel (`dmesg`).
    ```bash
    $ journalctl -k
    ```
*   **JSON (`-o json`):** Programmer script Python ELK.
    ```bash
    $ journalctl -u ssh -o json-pretty
    ```

@section: 6. `dmesg`: Hardware Whisper

Command read "Kernel Ring Buffer". Hardware drivers before disk mount logging start.

```bash
$ sudo dmesg
```
Pipe (`| less`) or color `-H`:
```bash
$ sudo dmesg -H
```

**Case: Phantom USB**
Connect USB nothing. Broken? Port? Driver?
1.  Term `sudo dmesg -w` (wait/follow).
2.  Unplug.
3.  Plug.
4.  Look.
    *   `New USB device... sdb: sdb1`, hardware ok port ok system see. Soft mount prob.
    *   `USB error` `descriptor read error` `I/O`, hardware damaged.
    *   NOTHING, port dead electric.

@section: 7. Lab: Detective

Simulate problem logs.

**Scenario:** User complains `sudo` hate.

1.  **Reproduce:**
    Sudo fail pass.
    ```bash
    $ sudo ls /root
    [sudo] password: (wrong)
    Sorry.
    (Fail 3)
    sudo: 3 incorrect
    ```

2.  **Evidence (Classic):**
    `auth.log`.
    ```bash
    $ sudo tail /var/log/auth.log
    ```
    See `FAILED su root...` `sudo: 3 incorrect`. Proof.

3.  **Evidence (Modern):**
    Journalctl path.
    ```bash
    $ journalctl /usr/bin/sudo | tail
    ```
    Systemd smart path.

4.  **Monitor (Trap):**
    Term `journalctl -f`.
    Other window `sudo` correct.
    See line: `COMMAND=/usr/bin/ls /root`.
    Success logged too.

@section: 8. Journal Maint

Binary logs grow. Unlike `logrotate`, `journalctl` manages own.

Usage:
```bash
$ journalctl --disk-usage
1.2G
```

Clean manual (Vacuum):

```bash
# Keep 2 days
$ sudo journalctl --vacuum-time=2d

# Keep 500MB
$ sudo journalctl --vacuum-size=500M
```

@section: Summary / Cheat Sheet

Table saves life.

| Goal | Classic | Modern |
| :--- | :--- | :--- |
| **All (page)** | `less /var/log/syslog` | `journalctl` |
| **End** | `tail /var/log/syslog` | `journalctl -e` |
| **Realtime** | `tail -f /var/log/syslog` | `journalctl -f` |
| **Service (SSH)** | `grep ssh /var/log/auth.log` | `journalctl -u ssh` |
| **Errors** | `grep "Error" ...` | `journalctl -p err` |
| **Prev Boot** | (Hard) | `journalctl -b -1` |
| **Kernel**| `dmesg` | `journalctl -k` |

**Final Wisdom:**
Linux fail, **NO REBOOT** Windows.
Reboot erases clues (`dmesg`).
1.  Logs.
2.  End.
3.  Error msg copy.
4.  Google.
Log diff "Broken" "I fix".