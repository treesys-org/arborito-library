@title: Process Control: The Orchestra Conductor (ps, top, kill)
@icon: 🚦
@description: The definitive guide (+500 lines) to mastering what happens in your CPU. Learn to read 'top', understand PIDs, kill zombies, and manage background processes.
@order: 4

# The Orchestra Conductor: Mastering Process Management

Welcome to your computer's command center.

So far, we treated files as static things living on disk. But off computer full disk useless. Magic happens files "come alive".

Run program (Firefox, Python, `ls`), OS takes dead code disk, loads RAM, assigns resources, gives turn CPU. Moment, program stops "program" becomes **Process**.

Linux system bustling city. Right now, hundreds processes running.
*   Working frantically (browser rendering).
*   Sleeping, waiting key press.
*   Invisible "daemons" watching network time.

As SysAdmin, you **Orchestra Conductor** (Mayor).
Absolute power:
1.  See consuming resources (CPU/RAM).
2.  Decide live die (Kill).
3.  Change priorities (VIP).
4.  Send tasks background.

Massive guide, dissect process anatomy, "Load Average", kill polite (not polite), multitasking terminal.

Prepare. Look inside machine brain.

@section: 1. Process Anatomy

Before scalpel, understand patient.
Kernel, process not "Firefox". Data structure specific properties.

### 1.1 ID: PID (Process ID)
Human world names. Kernel world names irrelevant. **PID**.

Process born gets unique number: **Process ID**.
*   Positive integer.
*   Assigned sequentially.
*   Process **number 1** always **systemd** (init). "Father of All". Kill 1, system dies (Kernel Panic).
*   Limit reached (32768+), counter restarts, skipping occupied.

**Golden Rule:** Control process, need PID.

### 1.2 Family Tree: PPID (Parent Process ID)
Processes not spontaneous (except 1).
Process created by process.
*   Open terminal (`bash`) run `firefox`, `bash` FATHER `firefox`.
*   **PPID** Firefox is PID Bash.

Hierarchy vital. Kill father, children die, or orphans (later).

### 1.3 Owner: UID & GID
Process belongs user (User ID) group (Group ID).
Determines capability.
*   Run `cat /etc/shadow` normal user, `cat` born your UID. Try read file, Kernel sees UID no perm, fails.
*   Run `sudo cat /etc/shadow`, process born UID 0 (root), success.

@section: 2. `ps`: The Snapshot

Command `ps` (Process Status) ancient standard tool.
Camera: shows state instant pressed Enter.

### 2.1 Useless `ps`
Write `ps`:
```bash
$ ps
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 5678 pts/0    00:00:00 ps
```
Useless. Shows processes **you** running **that terminal**.

### 2.2 Gold Standard: `ps aux`
See EVERYTHING, BSD options (no dashes). Memorize: **`ps aux`**.

*   `a`: All processes (all), not just yours.
*   `u`: User format (user), details %CPU Mem.
*   `x`: Processes no terminal (daemons/background).

Run:
```bash
$ ps aux | head -n 5
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 168436 13244 ?        Ss   oct20   0:05 /sbin/init
root         2  0.0  0.0      0     0 ?        S    oct20   0:00 [kthreadd]
...
```

### 2.3 Deciphering `ps aux` Cols
Dense table. Explain col col, truth lies here.

1.  **USER:** Owner. (`root`, `www-data`, `john`).
2.  **PID:** Unique ID. (`1234`).
3.  **%CPU:** Percentage processor using *now*.
4.  **%MEM:** Percentage physical RAM.
5.  **VSZ (Virtual Memory Size):** Virtual memory *reserved*.
    *   *Note:* Giant liar number. Program ask "reserve 1GB" use 1MB. VSZ 1GB.
6.  **RSS (Resident Set Size):** Real physical RAM using. **Matter number** running out RAM.
7.  **TTY:** Terminal running. `?` daemon.
8.  **STAT:** State (See States below).
9.  **START:** When started.
10. **TIME:** Total CPU time consumed life.
11. **COMMAND:** Exact command launched (args).

### 2.4 Alternative: `ps -ef`
Corporate world (Unix/Red Hat), `ps -ef`.
*   `-e`: Every.
*   `-f`: Full format.

Similar `aux` different order shows **PPID** clearly, useful hierarchy.

@section: 3. Filtering Noise (`grep`)

`ps aux` spits 300 lines. Want know web server "nginx" running.
Pipes.

```bash
$ ps aux | grep nginx
```

Result:
```text
root      1050  0.0  0.1  ... nginx: master process /usr/sbin/nginx
www-data  1051  0.0  0.2  ... nginx: worker process
john      2040  0.0  0.0  ... grep --color=auto nginx
```

**Grep ghost:**
Last line. `grep` searching self!
Run `ps`, `grep` running, photo catches.
Ninja trick:
```bash
$ ps aux | grep [n]ginx
```
(Search string "nginx", process name "grep [n]ginx", no match self. Regex magic).

@quiz: Analyzing server out RAM. Which `ps aux` column reliable real physical RAM consumption?
@option: VSZ
@correct: RSS
@option: TTY
@option: TIME

@section: 4. `top`: Live Monitor

`ps` photo. `top` video.
Updates 3 secs (default). Dashboard admin open secondary screen.

Run:
```bash
$ top
```

### 4.1 Header (System Summary)
First 5 lines vital info health.

**Line 1: Uptime Load Average**
`top - 10:00:01 up 15 days, 2 users, load average: 0.50, 1.10, 1.20`
*   **up 15 days:** Server 15 days no reboot.
*   **load average:** CRITICAL. Three numbers load avg last **1 min**, **5 min**, **15 min**.

**What is Load Average? (Bridge Analogy)**
Bridge (CPU).
*   Load **0.0**, bridge empty.
*   Load **0.5**, bridge 50%. Flow.
*   Load **1.0**, bridge full, flow.
*   Load **2.0**, bridge full AND queue cars same size waiting.

**Rule:** Load 1.0 means CPU 100%.
*   4 cores, Load 4.0 100%. Load 2.0 50%.
*   Numbers down (1.20 -> 1.10 -> 0.50), fixing.
*   Numbers up (0.50 -> 1.10 -> 5.00), run!

**Line 2: Tasks**
Total processes, running, sleeping, zombie.

**Line 3: %Cpu(s)**
*   `us` (User): User programs (browser).
*   `sy` (System): Kernel tasks.
*   `id` (Idle): Scratching belly (free).
*   `wa` (Wait I/O): **IMPORTANT!** CPU stopped waiting hard drive. High number, disk slow/broken.

### 4.2 Interacting `top`
Control keys:
*   **M** (Shift+m): Sort **Memory**. (Find RAM leaks).
*   **P** (Shift+p): Sort **CPU** (Default).
*   **k**: Kill process. Ask PID.
*   **1**: Multiple cores, breakdown one by one.
*   **q**: Exit.

### 4.3 `htop`: Evolution
`top` ugly old. Install (`sudo apt install htop`), do it.
**`htop`** colors, bars, mouse, human interface. Preferred today.

@section: 5. Life Cycle: Process States

Col STAT `ps` `top`, weird letters.

1.  **R (Running):** CPU or ready.
2.  **S (Sleeping):** Sleeping. Waiting event (network packet, key). Most.
3.  **D (Uninterruptible Sleep):** "Dangerous". Waiting Hardware (Disk) **cannot be interrupted**. `kill -9` fails. Wait hardware response (or reboot if broken).
4.  **Z (Zombie):** Undead.
    *   Finished work dead, "Father" not read will (exit code).
    *   No CPU/RAM. Occupy PID table entry.
    *   Cannot kill (dead). Clean, kill Father.

@quiz: Process state 'D' blocking system. Try `kill -9` stays. Why?
@option: Need root.
@option: Virus.
@correct: Process 'Uninterruptible Sleep' waiting hardware (I/O); cannot process signals until hardware responds.
@option: Zombie.

@section: 6. `kill`: Art Digital Murder

Program hangs. Eats memory. Terminate.
Command `kill` misleading. `kill` **sends signals**.

**Syntax:**
`kill [SIGNAL] PID`

Dozens signals, memorize 4:

### 1. SIGTERM (Signal 15) - "Please end"
Default.
*   Polite. *"Hey, please close files, save, exit"*.
*   Program ignore or delay.
*   **Use:** `kill 1234`

### 2. SIGKILL (Signal 9) - "Sniper"
Brute force.
*   Not sent program. Kernel intercepts **destroys** process instantly.
*   Program clean nothing, save nothing. Corrupt files.
*   **Use:** `kill -9 1234`
*   *Tip:* Use if SIGTERM fails.

### 3. SIGINT (Signal 2) - "Interrupt"
Same `Ctrl + C`. Abrupt stop.

### 4. SIGHUP (Signal 1) - "Hangup Reload"
History "Hang Up" phone/modem.
Today daemons (web servers): *"Don't die, reread config"*.
*   **Use:** `kill -1 1234` (Apply config changes no downtime).

### Killing Name: `pkill` `killall`
Find PID `ps` copy `kill` slow.

*   **`pkill`**: Partial name match.
    `pkill fire` (Kills firefox).
*   **`killall`**: EXACT name match.
    `killall firefox`
    **DANGER KILLALL!** Unix commercial (Solaris, AIX), `killall` **"Kill All Processes"** (System wipe). Enterprise servers careful. Linux safe.

@section: 7. Jobs: Foreground Background

Terminal single-thread: run command wait. Linux multitask. Launch background.

### Ampersand `&`
Add `&` end, **Background**. Control return instantly.

```bash
$ sleep 60 &
[1] 4567
```
Response: `[1]` (Job Num) `4567` (PID).

### Controlling Jobs
Run long script no `&` block terminal. Cancel restart? **No.**

1.  **Pause:** `Ctrl + Z`.
    *   Stop (freeze, T state) background.
    *   `[1]+  Stopped                 ./long_script.sh`
2.  **View jobs:** `jobs`.
    *   List tasks terminal.
3.  **Resume Background (`bg`):**
    *   `bg %1`.
    *   Process 1 wakes working background (`&`). Use terminal.
4.  **Bring Front (`fg`):**
    *   `fg %1`.
    *   Process front takes terminal control.

**Logout problem (nohup):**
Background process close terminal (SSH cut), **process dies**. (SIGHUP).
Avoid run eternal:
```bash
$ nohup ./long_script.sh &
```
`nohup` (No Hang Up) immune SIGHUP. Output `nohup.out`.

@section: 8. Priorities: Nice Renice

Not all processes equal. Video render CPU max, or slow background.

Linux value **Niceness**.
*   **-20** (Selfish, max prio) to **+19** (Nice, min prio).
*   Default **0**.
*   **Rule:** Higher number, "nicer" (yields). Lower (negative), higher prio.

**Launch priority (`nice`):**
```bash
# Backup low priority (very nice) don't disturb
$ nice -n 19 tar -czf backup.tar.gz /home
```

**Change flight (`renice`):**
```bash
# Game max priority (PID 555)
# Only root lower value (more priority).
$ sudo renice -n -10 -p 555
```

@section: 9. Practical Lab: Immortal Process

Play life death.

1.  **Create victim:**
    Open terminal run eternal do nothing:
    ```bash
    $ sleep 1000
    ```
    Blocked.

2.  **Pause:**
    `Ctrl + Z`. "Stopped".

3.  **Send background:**
    `bg`. Running background.

4.  **Verify:**
    `jobs`. "Running".
    `ps aux | grep sleep`. See PID.

5.  **Soft Murder:**
    `kill [PID]`.
    `jobs`. "Terminated".

6.  **Zombie Murder (Theory):**
    Kill process stays `<defunct>` `ps`, zombie. Don't kill more. Find PPID `ps -ef` kill father. System clean son.

@section: Summary / Cheat Sheet

| Command | Action | Key Trick |
| :--- | :--- | :--- |
| `ps aux` | Photo all processes | Col PID RSS (RAM). |
| `top` | Realtime monitor | "Load Average". |
| `htop` | Improved monitor | Install. |
| `kill PID` | Terminate (SIGTERM 15) | Polite. |
| `kill -9 PID` | Kill (SIGKILL 9) | Brute force. If 15 fail. |
| `Ctrl + C` | Cancel current | SIGINT. |
| `Ctrl + Z` | Pause current | Then `bg` `fg`. |
| `jobs` | View jobs terminal | |
| `bg %N` | Resume back | |
| `fg %N` | Bring front | |
| `nice` | Run prio | `nice -n 19` heavy back tasks. |

Congrats! Manage resources better than OS itself. Owner CPU time.