
@title: Systemd Deep Dive: The System Architect
@icon: 🚀
@description: The definitive and exhaustive guide on PID 1. From Unit anatomy and dependency graphs to Cgroups, boot optimization, and creating production daemons.
@order: 1

# The Architect of Chaos: Mastering Systemd

@section: LPIC-2 map — Module 1 (systemd, storage, kernel, automation)

Typical **LPIC-2 (200 series)** alignment: boot/recovery targets, LVM, kernel parameters, timers vs cron, `journalctl`.

**RHEL exam style:** `systemctl`, `journalctl`, and troubleshooting units are core; same tools on modern Debian/Ubuntu with systemd.

Welcome to the major leagues of Linux system administration.

At the Junior level, you learned that typing `sudo systemctl start apache2` turns on the web server. That's like knowing how to turn a car key. You know how to drive it, but you have no idea what's happening under the hood. If the car doesn't start when you turn the key, you are lost.

At this Advanced level, we are going to open the hood, disassemble the engine piece by piece, and learn how to tune it.

**Systemd** is, without a doubt, the most important, controversial, and powerful component of the modern Linux ecosystem. It is no longer just an "init system" that starts processes when the PC turns on. It has become a massive system administration suite that has absorbed critical responsibilities that were previously scattered:
*   Log management (**journald**).
*   Device management (**udev**).
*   Network management (**networkd**).
*   DNS resolution (**resolved**).
*   Schedules and timers (**timers**).
*   Lightweight containers (**nspawn**).
*   User session management (**logind**).

For a Senior SysAdmin, understanding Systemd is not optional. It is the connective tissue that holds the operating system together. It is the first process born (**PID 1**) and the last to die. If Systemd fails, the Kernel panics and the system dies.

In this monumental guide, we will dissect the anatomy of a Unit, understand the complex dependency graph that allows the system to boot in parallel in seconds, analyze boot time to the millisecond, learn to limit resources with Cgroups so a process doesn't freeze your server, and write our own armored services for production.

Get ready. We are going to master the God of the machine.

@section: 1. History and Philosophy: Why Systemd?

To understand Systemd, you have to understand what came before and why it was a mess.

### The Old King: SysVinit
For decades, Linux used **SysVinit**. It was simple. It was a collection of Bash scripts in `/etc/init.d/`.
To boot the system, SysVinit executed these scripts **one by one**, in sequential order.
1.  Start Network. (Wait for it to finish).
2.  Start Disk. (Wait).
3.  Start Database. (Wait).
4.  Start Web.

**The Problem:**
*   **Slowness:** If the network took 10 seconds to get an IP, the entire boot stopped for 10 seconds. Modern CPUs have many cores, but SysVinit only used one. It was a waste.
*   **Fragility:** Bash scripts are prone to errors, hard to maintain, and vary between distributions.
*   **Lack of Control:** If a service died, SysVinit didn't always know or couldn't restart it.

### The Revolution: Systemd
Lennart Poettering designed Systemd with a radical idea: **Aggressive Parallelism**.
Systemd doesn't start things in a line. It starts everything at once.

How is this possible? How do you start the Web Server before the Database is ready?
Using **Sockets**.
1.  Systemd creates the "socket" (plug) for the Database immediately.
2.  It starts the Web Server and the Database **simultaneously**.
3.  The Web Server tries to connect to the Database. Since the socket already exists, the connection waits (buffer) instead of failing.
4.  When the Database finishes booting, it picks up the pending connections from the socket and processes them.

Result: A system that boots in 5 seconds instead of 60.

@section: 2. The Atom of Systemd: The Unit

In Systemd, we don't manage "files" or "scripts". We manage **Units**.
Any resource that Systemd knows how to handle is encapsulated in a configuration file called a Unit File.

### Types of Units
The system recognizes the unit type by its extension. You must know the main ones:

1.  **`.service`**: The most important. Describes a process or application (Nginx, Docker, SSH). If you want to run a program, you use a service.
2.  **`.socket`**: A communication socket (network or IPC file). Systemd can listen on a port and start a service only when traffic arrives (Socket Activation).
3.  **`.target`**: A logical group of units. Used to synchronize boot up. (e.g., `multi-user.target` is the group of services for multi-user mode).
4.  **`.timer`**: A timer. Replaces Cron. Activates a `.service` at a specific date/time.
5.  **`.mount`**: A file system mount point. Replaces or complements `/etc/fstab`.
6.  **`.automount`**: A mount point that only mounts when someone tries to access it.
7.  **`.device`**: A hardware device detected by the kernel.
8.  **`.path`**: Allows monitoring a file or directory and activating a service if it changes.

### Where Units Live
There is a strict hierarchy. Systemd looks for files in this order. If there are files with the same name, the top one wins (overrides the bottom one).

1.  **`/etc/systemd/system/`**: **HIGH Priority**. This is where you, the Administrator, work. Anything you put here rules over the rest of the system.
2.  **`/run/systemd/system/`**: **MEDIUM Priority**. Units created dynamically at runtime. Deleted on reboot.
3.  **`/usr/lib/systemd/system/`**: **LOW Priority**. This is where the package manager (`apt`, `dnf`) installs default units.
    *   **GOLDEN RULE:** **NEVER** edit a file in `/usr/lib/...`. If you do, the next time you update the package (e.g., `apt upgrade nginx`), the system will overwrite your file and you will lose your changes. You must always copy it to `/etc/` or use *overrides*.

@section: 3. Anatomy of a Service: Forensic Dissection

Let's open a real and complex `.service` file to understand each line. We will use `sshd.service` (the SSH server) as a patient.

To see the content of the unit currently loaded in memory:
```bash
$ systemctl cat sshd.service
```

You will see something like this (I have added explanatory comments):

```ini
# [Unit] SECTION: Metadata and Dependencies
[Unit]
Description=OpenSSH Daemon
Documentation=man:sshd(8) man:sshd_config(5)

# Boot Order (Ordering)
# "Start after the network and audit system are ready"
After=network.target auditd.service

# Dependencies (Dependencies)
# "I would like sshd-keygen.target to start too, but if it fails, I start anyway"
Wants=sshd-keygen.target
# "I NEED sysinit.target to have finished successfully. If it fails, I fail."
Requires=sysinit.target

# [Service] SECTION: How to run the process
[Service]
# Service type (Vital to understand this)
# 'notify' means the service will actively notify systemd when it is ready.
Type=notify

# Environment variable file (optional)
EnvironmentFile=-/etc/default/ssh

# The main command. MUST be an absolute path.
ExecStart=/usr/sbin/sshd -D $SSHD_OPTS

# What to do if the admin asks to reload
ExecReload=/bin/kill -HUP $MAINPID

# What to do if the process dies
KillMode=process
Restart=on-failure
RestartSec=42s

# [Install] SECTION: When to activate
[Install]
# "Activate me when the system reaches multi-user level"
WantedBy=multi-user.target
```

### Deep Dive into `[Unit]`
This defines the service's place in the universe.

*   **`After=` / `Before=`**: Defines **ORDER** only, not requirement.
    *   `After=network.target` means: "If the network and I are going to start at the same time, put me in the queue after the network". It does NOT mean "I need the network". If the network doesn't start, SSH will start anyway.
*   **`Requires=`**: STRONG dependency. If the listed unit fails or is not active, this unit will fail immediately. It is very strict.
*   **`Wants=`**: WEAK dependency. Recommended. Systemd will try to start the other unit, but if it fails, nothing happens. Your service continues.
*   **`BindsTo=`**: If the other unit dies suddenly, this unit will also die. Useful for services linked to hardware (if I disconnect the network card, turn off the VPN service).

### Deep Dive into `[Service]`
Here is the meat.

*   **`Type=`**:
    *   `simple`: (Default). Systemd assumes the service is active the moment it launches the process. Fast, but if the process takes time to initialize, dependents might fail.
    *   `forking`: For classic daemons that duplicate themselves in the background (daemonize). Systemd waits for the parent to die to consider the service active.
    *   `oneshot`: For scripts that execute a task and finish (backup, firewall). The service is considered "active" even if the process has finished (if you use `RemainAfterExit=yes`).
    *   `notify`: The most advanced. The service sends a signal to Systemd (`sd_notify`) to say "I'm ready!".
*   **`ExecStart=`**: The command. You cannot use pipes `|`, redirects `>`, or `&` here, because it does not run in a full shell. If you need that, use: `/bin/bash -c "command | filter > file"`.
*   **`User=` / `Group=`**: Which user runs the process. For security, never use `root` unless absolutely necessary.
*   **`Restart=`**: Resurrection policy.
    *   `no`: If it dies, it dies.
    *   `on-failure`: If it exits with error (code other than 0) or is killed, restart. If closed cleanly, no. (Recommended for production).
    *   `always`: Restart always, even if you tell it to stop. (Careful with infinite loops).

### Deep Dive into `[Install]`
This section is only read when you run `systemctl enable`.

*   **`WantedBy=`**: Says "who wants me".
    *   If you put `WantedBy=multi-user.target`, when enabling the service, Systemd creates a symbolic link in `/etc/systemd/system/multi-user.target.wants/` pointing to your service.
    *   When the system boots and reaches the `multi-user.target` goal, Systemd looks in that `.wants/` folder and starts everything inside. This is how automatic boot works.

@quiz: You are writing a service for a backup script that must run and finish, but you want Systemd to consider it "active" and successful once the script finishes. What combination do you use?
@option: Type=simple
@correct: Type=oneshot with RemainAfterExit=yes
@option: Type=forking
@option: Type=notify

@section: 4. Controlling the Beast: `systemctl`

You already know `start`, `stop`, and `restart`. Let's see the commands of an advanced SysAdmin.

### Reloading without stopping (`reload`)
Many services (Apache, Nginx, SSH) know how to read their configuration again without killing active connections.
```bash
$ sudo systemctl reload nginx
```
Use this whenever you change a configuration. `restart` is aggressive (kill and start), `reload` is gentle. If the service doesn't support reload, systemctl will tell you.

### Real status (`status`)
`systemctl status` gives you a lot of information. Read it well.
*   **Loaded:** Is the unit file loaded? Where is it (`/lib/...` or `/etc/...`)? Is it `enabled` (auto-start) or `disabled`?
*   **Active:**
    *   `active (running)`: All good.
    *   `active (exited)`: Normal for `oneshot` services. Finished well.
    *   `inactive (dead)`: Stopped.
    *   `failed`: Died with error.
*   **Main PID:** The main process number.
*   **Logs:** The last 10 lines of the log (journal) for that service. Vital to see why it failed.

### Masking (`mask`)
Sometimes, `disable` is not enough. If you disable a service, another service could wake it up if it has a `Wants=` dependency on it.
If you want to ensure a service **NEVER** starts under any circumstances (e.g., to avoid serious conflicts or for security):

```bash
$ sudo systemctl mask nginx
```
This creates a symbolic link from the unit to `/dev/null`. Systemd will think the unit is empty. Any attempt to start it (manual or automatic) will fail. To recover it: `unmask`.

@section: 5. Editing Units: The "Drop-In" Method

Imagine you want to change the user a service runs as, or add an environment variable.
**NEWBIE MISTAKE:** Open `/usr/lib/systemd/system/service.service` with nano and edit it.
You will lose changes on the next update.

**THE PRO WAY:** Use "Drop-In Overrides".
Systemd allows creating files that "patch" the original configuration without touching it.

```bash
$ sudo systemctl edit nginx
```
This will open your text editor and create a temporary file. Anything you write there will be saved in a special file `/etc/systemd/system/nginx.service.d/override.conf`.

Example: We want to add a memory limit and change the description.
```ini
[Unit]
Description=Nginx Web Server (Custom)

[Service]
MemoryMax=500M
```
Systemd will merge this with the original file. Your configuration takes precedence.
To see the final merged configuration:
```bash
$ systemd-delta
# or
$ systemctl cat nginx
```

**Reload Daemon:**
Whenever you touch unit files on disk manually (without using `edit`), you must notify Systemd:
```bash
$ sudo systemctl daemon-reload
```
If you don't, Systemd will use the cached version and ignore you.

@section: 6. Targets: 21st Century Runlevels

In SysVinit there were "Runlevels" (0 to 6). In Systemd there are **Targets**. They are more flexible because they can inherit and combine.

Essentials:
*   **`poweroff.target` (Runlevel 0):** Shutdown.
*   **`rescue.target` (Runlevel 1):** Rescue mode. File system mounted, but no network and with root shell.
*   **`multi-user.target` (Runlevel 3):** Standard for servers. Network, multi-user, no graphical environment.
*   **`graphical.target` (Runlevel 5):** Desktop mode. Same as above + Display Manager.
*   **`reboot.target` (Runlevel 6):** Reboot.
*   **`emergency.target`:** Absolute minimum. Read-only file system. Use if `rescue` fails.

### Changing state
To pass to maintenance mode (no network, root only) without rebooting:
```bash
$ sudo systemctl isolate rescue.target
```
*Careful: `isolate` stops immediately any service that is not a dependency of the new target. If you are on SSH and isolate to a target without network, it will kick you out.*

### Changing default boot
Do you have a server with Ubuntu Desktop but want it to boot in text mode to save RAM?
```bash
$ sudo systemctl set-default multi-user.target
```
On the next reboot, it won't load the graphical interface (Gnome/KDE), saving 1GB of RAM.

@section: 7. Performance Analysis (Boot Profiling)

Does your server take 2 minutes to boot? Systemd knows who is guilty.

### Overview
```bash
$ systemd-analyze
Startup finished in 3.4s (kernel) + 12.1s (userspace) = 15.5s
```

### The List of Shame (`blame`)
This command sorts services by the time they took to initialize.
```bash
$ systemd-analyze blame
8.2s network-online.target
4.1s docker.service
1.2s apt-daily.service
...
```
If you see something taking too long and don't need it, `disable` or `mask`.

### The Critical Chain (`critical-chain`)
Sometimes `blame` lies. A service might take 5 seconds but run in parallel without bothering anyone.
What matters is the **Critical Chain**: the sequence of blocking services that delay the moment the system is 100% ready.
```bash
$ systemd-analyze critical-chain
```
It will show you a red tree with the real culprits of the delay.

### Vector Graphs
If you want to impress your boss, generate an SVG graph of the entire boot process:
```bash
$ systemd-analyze plot > boot.svg
```
Open that file with your browser and you will see a spectacular timeline of each process.

@section: 8. Cgroups: Controlling Resources

Systemd uses **Cgroups** (Control Groups) from the kernel to group processes. This allows it to do something incredible: **Limit resources by service**.

Imagine you have a backup script (`backup.service`) that sometimes goes crazy, uses 100% CPU, and hangs your database.
You can put a leash on it.

```bash
$ sudo systemctl edit backup.service
```

```ini
[Service]
# Hard CPU limit (20% of one core)
CPUQuota=20%
# Memory limit (if it exceeds 1GB, the kernel kills it)
MemoryMax=1G
# Disk priority (low)
IOWeight=10
```

Restart the service. Now, whatever that script does, it will **never** exceed 20% CPU. The kernel will throttle it. It is a lifesaver for shared environments.

### Monitoring with `systemd-cgtop`
To see resource consumption grouped by services (not by individual processes like `top`):
```bash
$ systemd-cgtop
```
You will see which service (and all its children) is consuming the most.

@section: 9. Systemd-run: Ad-Hoc Commands

Sometimes you want to run a heavy command (like a compilation or a huge `find`) but want Systemd advantages (logs, resource limits, background) without creating a `.service` file.
Use `systemd-run`.

```bash
# Run a command named 'my-task', memory limit and in background
$ sudo systemd-run --unit=my-task --property=MemoryMax=500M /path/to/script.sh
Running as unit: my-task.service
```

Now that task runs managed by systemd.
*   See logs: `journalctl -u my-task -f`
*   Stop: `systemctl stop my-task`
*   See status: `systemctl status my-task`

Even if you close your terminal, the task keeps running securely.

@section: 10. Lab: The Immortal Service

We are going to create a Python service that is resilient to failures.

**Step 1: The Script (`/usr/local/bin/immortal.py`)**
```python
#!/usr/bin/env python3
import time
import sys

# We flush so logs go instantly to systemd
print("The Immortal service has been born...", flush=True)

try:
    while True:
        print("Still alive...", flush=True)
        time.sleep(5)
except KeyboardInterrupt:
    print("They are killing me...", flush=True)
    sys.exit(0)
```
Give permissions: `chmod +x /usr/local/bin/immortal.py`

**Step 2: The Unit (`/etc/systemd/system/immortal.service`)**
```ini
[Unit]
Description=Immortal Test Service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/immortal.py
# Restart policy: If it dies, revive it.
Restart=always
# Wait 3 seconds before reviving (to not saturate CPU if loop fails)
RestartSec=3

[Install]
WantedBy=multi-user.target
```

**Step 3: Activation**
```bash
$ sudo systemctl daemon-reload
$ sudo systemctl start immortal
$ sudo systemctl enable immortal
```

**Step 4: Chaos Test**
Let's assassinate the process to see if Systemd keeps its promise.
1.  Find the PID: `systemctl status immortal` (look for "Main PID").
2.  Kill it: `sudo kill -9 [PID]`.
3.  Check immediately: `systemctl status immortal`.

You will see the status is `active (running)`, but the **PID has changed**. Systemd detected the death and launched a new copy in less than 3 seconds.
Look at logs: `journalctl -u immortal`. You will see the moment of death and resurrection.

@section: Summary / Cheat Sheet

| Command | Action |
| :--- | :--- |
| `systemctl daemon-reload` | **Mandatory** after editing any `.service` file. |
| `systemctl edit [unit]` | Safe and persistent way to modify a service. |
| `systemctl mask [unit]` | Disable a unit permanently (points to /dev/null). |
| `systemctl list-dependencies` | Shows requirement tree. |
| `systemd-analyze blame` | Shows which services take longest to boot. |
| `systemd-cgtop` | Resource monitor by service/cgroup. |
| `journalctl -xeu [unit]` | See detailed logs and errors for a specific unit. |
| `systemd-run` | Run ad-hoc commands as temporary services. |

Systemd has a steep learning curve, but once you master it, it gives you control over the operating system that old Unix administrators could only dream of. Now you are the true owner of PID 1.
