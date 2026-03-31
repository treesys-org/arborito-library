
@title: System Watchmaker: Cron and Systemd Timers
@icon: ⏰
@description: Advanced temporal automation. From crontab syntax to Systemd Timers precision, managing asynchronous tasks with 'at' and 'systemd-run'.
@order: 5

# The System Watchmaker: Temporal Automation

Server requiring human push buttons maintenance not server, pet.
Rule number one SysAdmin: **Do twice, automate.**

Linux ancient modern tools execute tasks time.
*   "Backup 3 AM".
*   "Clean temps Friday".
*   "Run script 15 mins boot".
*   "Shutdown 2 hours".

Module, master classic **Cron**, understand fails mysteriously (damn environment), learn superior modern tool manages time surgical precision: **Systemd Timers**.

@section: 1. Cron: Immortal Classic

Daemon `cron` (Chronos Greek time god) software ancient reliable Unix. Wakes minute, checks lists, executes. Simple, effective, everywhere.

### 1.1 Cron Architecture
Service `cron` `crond`.
Reads config files **crontabs**.

Three main places live tasks, vital not confuse:

1.  **User Cron (`crontab -e`):**
    *   User private table.
    *   Edit command `crontab -e` (never edit files hand).
    *   Tasks execute user permissions (UID/GID).
    *   Cannot damage system (unless root).
    *   Real files `/var/spool/cron/crontabs/`.

2.  **System Cron (`/etc/crontab`):**
    *   Global unique file.
    *   Root touch.
    *   **Key diff:** Extra column specify *user* execute command.

3.  **Modular Cron (`/etc/cron.d/`):**
    *   Directory drop text files.
    *   Works `/etc/crontab` (needs user).
    *   Method install scripts packages, no edit existing, add new.

### 1.2 Cursed (Blessed) Syntax
Cron line 5 time fields command. System cron 6 (user before command).

User structure:
`m h dom mon dow command`

System structure:
`m h dom mon dow user command`

1.  **m**: Minute (0-59).
2.  **h**: Hour (0-23).
3.  **dom**: Day Month (1-31).
4.  **mon**: Month (1-12).
5.  **dow**: Day Week (0-7, 0 7 Sunday).

**Special Operators:**
*   `*` (Asterisk): "All". Hour field "every hour".
*   `,` (Comma): List. `1,15,30` minute 1, 15, 30.
*   `-` (Hyphen): Range. `1-5` day 1 to 5.
*   `/` (Slash): Step. `*/10` "every 10 units".

**Decipher Examples:**

*   `30 04 * * * backup.sh`
    *   "04:30, any day month, any month, any day week".
    *   Summary: **Daily 4:30 AM**.

*   `00 17 * * 5 email.sh`
    *   "17:00, any day, any month, day week 5 (Friday)".
    *   Summary: **Every Friday 5 PM**.

*   `*/10 * * * * check.sh`
    *   "Minute divisible 10 (0, 10, 20, 30, 40, 50)".
    *   Summary: **Every 10 minutes**.

*   `00 00 1 * * bills.sh`
    *   "Midnight (00:00) day 1 any month".
    *   Summary: **Monthly, day 1**.

*   `00 09-18 * * 1-5 work.sh`
    *   "Minute 0, hours 9 18, Monday (1) Friday (5)".
    *   Summary: **Hourly work hours**.

**Shortcuts (Keywords):**
Lazy, cron magic words start `@`:
*   `@reboot`: Once boot. (Useful!).
*   `@yearly` `@annually`: Once year (1 Jan).
*   `@monthly`: Once month.
*   `@weekly`: Once week.
*   `@daily` `@midnight`: Once day.
*   `@hourly`: Once hour.

@quiz: Fundamental difference editing cron `crontab -e` editing `/etc/crontab`?
@option: `crontab -e` root only.
@correct: `/etc/crontab` extra column specify user execute command, `crontab -e` assumes current user.
@option: `/etc/crontab` XML syntax.
@option: `crontab -e` restart service, `/etc/crontab` no.

@section: 2. Environment Hell

99% newbie "script works terminal not cron", fault **Environment**.

Open terminal login:
1.  Load `.bashrc` `.profile`.
2.  Define `$PATH` (programs where).
3.  Define alias, colors user vars.

Cron executes:
1.  **NO** load `.bashrc`.
2.  **NO** load profile.
3.  `$PATH` minimal (`/bin` `/usr/bin`).
4.  No interactive terminal (no `read` no input).

**Symptoms:**
*   Script uses `python` cron not find `/usr/local/bin/python`.
*   Script writes relative folder (`./logs`) fail cron not folder.
*   Script `$MY_API_KEY` defined `.bashrc` cron empty.

**Engineering Solutions:**

1.  **Absolute Paths ALWAYS:**
    *   Bad: `python3 script.py`
    *   Good: `/usr/bin/python3 /home/juan/scripts/script.py`

2.  **Define PATH crontab:**
    Vars start crontab file:
    ```bash
    PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
    SHELL=/bin/bash
    API_KEY=123456

    30 04 * * * /home/juan/backup.sh
    ```

3.  **Load profile manual (Dirty effective):**
    Script depends thousand profile things, force load:
    `30 04 * * * source /home/juan/.bashrc && /home/juan/backup.sh`

4.  **Output Redirection (Logging):**
    Cron sends local email output error. Nobody reads local mail. Script fails no redirect, **never know error**.
    *   Mandatory redirect STDOUT STDERR:
    `* * * * * /path/script.sh >> /var/log/my_app.log 2>&1`

@quiz: Backup script works manually, cron fails silently. Probable cause?
@option: Cron no root permissions.
@correct: Cron environment variables ($PATH) limited not find commands uses incorrect relative paths.
@option: Script no exec permissions.
@option: Cron no shell scripts, binaries only.

@section: 3. Anacron: For Sleepers

Cron assumes server 24/7.
Task 3 AM laptop off 11 PM sleep... **task lost**. Cron no memory. 3 AM laptop off, 9 AM on, 3 AM passed. Bad luck.

**Anacron** (Anachronistic Cron) solves.
Not execute exact minute, frequency (daily, weekly).

**Works:**
1.  PC boots, Anacron wakes.
2.  Checks timestamp file (`/var/spool/anacron/`).
3.  Asks: "Task 'daily' done today?".
4.  No (PC off), execute **now**.

**Random Delay:**
Avoid laptop slow boot 50 tasks catch up, Anacron `RANDOM_DELAY`. Wait minutes start.

Folders `/etc/cron.daily`, `/etc/cron.weekly` `/etc/cron.monthly` managed Anacron desktop Ubuntu Fedora. Script `cron.daily` execute once day, regardless PC off night.

@section: 4. Systemd Timers: Superior Evolution

Cron great, 70s tech. Limits.
*   Precision 1 minute (no every 10 seconds).
*   Log dispersed.
*   No dependency ("Run if network online").

**Systemd Timers** replace cron modern.
Concept diff: No table. **Units**.
Two files task:
1.  **Service (`.service`):** WHAT runs.
2.  **Timer (`.timer`):** WHEN runs.

### Step 1: Create Service
Execute `backup.sh`.
Create `/etc/systemd/system/my-backup.service`:

```ini
[Unit]
Description=Backup Service Timer

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```
*(Note: No `[Install]` enable boot, timer wakes).*

### Step 2: Create Timer
Create `/etc/systemd/system/my-backup.timer`:

```ini
[Unit]
Description=Run backup daily 4 AM

[Timer]
# Cron equiv: "4 am daily"
OnCalendar=*-*-* 04:00:00

# PRECISION ENERGY
# Allow delay 10 min group CPU wakeups
AccuracySec=10m

# PERSISTENCE (Systemd "Anacron")
# PC off, execute immediately boot
Persistent=true

# RANDOM DELAY (Anti "Thundering Herd")
# 1000 servers timer, attack backup server once
RandomizedDelaySec=5m

[Install]
WantedBy=timers.target
```

### Step 3: Activate
```bash
$ sudo systemctl enable --now my-backup.timer
```
*(Activate timer, not service).*

### Timer Event Types
Systemd two time modes:

1.  **Monotonic (Relative):** Events, not clock. Immune time changes zones.
    *   `OnBootSec=15min`: 15 mins after boot.
    *   `OnUnitActiveSec=1h`: 1 hour last run. (Loop clean).

2.  **Calendar (Realtime):** Date/time (Wall Clock).
    *   `OnCalendar=Mon..Fri 10:00`: Mon Fri 10.
    *   `OnCalendar=*-*-01 00:00:00`: Day 1 month.
    *   Syntax: `DayWeek Year-Month-Day Hour:Min:Sec`.

### Advantages Systemd Timers Cron
1.  **Logs central:** `journalctl -u my-backup.service` stdout stderr, exact dates meta.
2.  **Precision:** Microseconds.
3.  **Dependencies:** `.service` `After=network-online.target` internet backup. No net, wait. Cron fail.
4.  **Resource Control (Cgroups):** Limit CPU (`CPUQuota`) RAM (`MemoryMax`) backup `.service`. Cron no easy.

**List Timers active:**
```bash
$ systemctl list-timers
```
Table:
*   **NEXT:** Next time.
*   **LEFT:** How long.
*   **LAST:** Last run.
*   **PASSED:** Time passed.
*   **UNIT:** Service active.

@quiz: Option Systemd `.timer` ensures task run computer off scheduled (Anacron behavior)?
@option: OnBootSec=true
@option: CatchUp=true
@correct: Persistent=true
@option: AlwaysRun=true

@section: 5. `at`: Butler Once

Don't repeat forever. "Shutdown 2 hours" "Run script 5 PM gone".
Cron useless (create delete task).
**`at`**.

Install: `sudo apt install at`.

### Interactive
```bash
$ at 5pm
warning: commands will be executed using /bin/sh
at> /home/juan/lights_off.sh
at> echo "Lights off" >> /tmp/log
at> (Ctrl+D save exit)
```
Ready. Response: `job 1 at Mon Oct 20 17:00:00 2025`.

### Pipes (Scripting)
Send tasks `at` no interactive:
```bash
$ echo "sudo apt update && sudo reboot" | at 03:00 tomorrow
```
Ideal reboot night.

### Flexible Time Syntax
`at` smart human time:
*   `at now + 10 minutes`
*   `at 4pm + 3 days`
*   `at 10:00 July 31`
*   `at teatime` (Yes, 16:00).

### Queue Management
*   **`atq`**: View pending.
*   **`atrm [ID]`**: Delete task (`atrm 5`).
*   **`batch`**: Special `at`. Run **only system load (Load Avg) < 0.8**. Heavy background (index DB) no slow server.

@section: 6. `systemd-run`: Modern Transient

Like `at` want Systemd power (logs, cgroups), `systemd-run`.
Transient units no file.

**Run now (encapsulated):**
```bash
$ sudo systemd-run --unit=my-compilation --property=MemoryMax=1G ./compile_all.sh
```
Return: `Running as unit: my-compilation.service`.
*   Background.
*   Terminal close, run.
*   Pass 1GB RAM, systemd kill.
*   Logs: `journalctl -u my-compilation -f`.
*   Stop: `systemctl stop my-compilation`.

**Schedule future (Ephemeral Timer):**
```bash
$ sudo systemd-run --on-active=1h /bin/touch /tmp/file
```
*(Execute 1 hour).*

@section: 7. Lab: Perfect Backup

Create robust backup system Systemd.

**Scenario:**
Compress config (`/etc`) save `/backups`.
*   Daily 02:00 AM.
*   Server off, boot.
*   Delete backups > 7 days.
*   Low CPU priority.

**1. Script (`/usr/local/bin/backup_etc.sh`)**
```bash
#!/bin/bash
DATE=$(date +%F)
DEST="/backups"

# Create dest
mkdir -p $DEST

# Compress date
tar -czf $DEST/etc-$DATE.tar.gz /etc 2>/dev/null

# Delete old (> 7 days)
find $DEST -name "etc-*.tar.gz" -mtime +7 -delete

echo "Backup complete $DATE"
```
*(`chmod +x`)*.

**2. Service (`/etc/systemd/system/etc-backup.service`)**
```ini
[Unit]
Description=Backup /etc

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup_etc.sh
# Make "nice" CPU
Nice=19
IOSchedulingClass=idle
```

**3. Timer (`/etc/systemd/system/etc-backup.timer`)**
```ini
[Unit]
Description=Daily trigger backup /etc

[Timer]
# 2 AM
OnCalendar=*-*-* 02:00:00
# Persist off
Persistent=true
# Random delay 5 min
RandomizedDelaySec=5m

[Install]
WantedBy=timers.target
```

**4. Activation**
```bash
$ sudo systemctl enable --now etc-backup.timer
```
*(Enable timer, not service. Timer wakes service).*

**5. Verify**
Check timer list ready:
```bash
$ systemctl list-timers
```
Ready! Professional resilient managed backup.

@section: Summary / Cheat Sheet

| Tool | Use Ideal | Command Key |
| :--- | :--- | :--- |
| **Cron** | Simple, recurrent, legacy. | `crontab -e` |
| **Anacron** | Daily tasks PCs off. | `/etc/anacrontab` |
| **Systemd Timers** | Complex, deps, logs, precision. | `systemctl list-timers` |
| **at** | Single future ("Do 5"). | `at 5pm` |
| **systemd-run** | Ad-hoc resource control. | `systemd-run ...` |

**Expert Tip:**
Quick personal, `crontab -e` king simplicity.
Infra, services, critical backups, production, migrate **Systemd Timers**. Visibility logs error control worth complexity.
