
@title: The Heart of the Beast: Kernel and Modules
@icon: 🔧
@description: Learn to manipulate the Kernel in real time with sysctl, manage modules with modprobe, understand Kernel Tainting, and the definitive guide to compiling your own Linux Kernel from scratch.
@order: 2

# The Heart of the Beast: Manipulation and Kernel Compilation

Welcome to the Holy Grail of Linux.

Until now, the Kernel (core) has been a "black box" for you. A file called `vmlinuz` that loads at the start, spits out some quick letters, and does magic so your mouse moves and your WiFi connects. But an advanced SysAdmin doesn't believe in magic. They believe in C code and resource management.

An advanced SysAdmin must know how to look inside that box, adjust it while the engine is running, and, if necessary, build a new custom part in the smithy.

In this massive module, we are going to demystify the most important component of your operating system. Not only will we learn to compile; we will learn to think like the Kernel.

@section: 1. Anatomy of the Kernel: Monolithic but Flexible

Before touching anything, we need to understand what we are touching.

### What exactly is the Kernel?
Imagine a symphony orchestra.
*   **Hardware** are the instruments (violins, trumpets, timpani).
*   **Applications** are the musicians (they want to play notes).
*   **The Kernel** is the **Conductor**.

Without the conductor, all musicians would try to play at once, fighting for the same space, and the result would be noise. The Kernel decides:
1.  Who plays now (CPU Management / Scheduler).
2.  Where everyone sits (Memory Management).
3.  How they communicate with instruments (Drivers).

### The Architecture: Modular Monolithic
There are two main philosophies of Kernel design:

1.  **Microkernel:** The core is tiny. It only handles basics (memory and CPU). Drivers (graphics card, hard drive) are separate processes running "outside" the core.
    *   *Advantage:* If the graphics driver fails, the system doesn't hang, it just restarts the driver.
    *   *Disadvantage:* Communication is slower.
    *   *Example:* MINIX, GNU Hurd.

2.  **Monolithic Kernel:** The core is giant. Everything (drivers, file system, network) lives inside the same memory space and executes with absolute privileges.
    *   *Advantage:* Extreme speed. Everything is right there.
    *   *Disadvantage:* If a driver fails (e.g., cheap Chinese printer driver), it can crash the whole system (Kernel Panic).
    *   *Example:* **Linux**.

**Wait, Linux is monolithic?**
Yes, but with a cool trick: it is **Modular**.
Although it is a single block, it allows loading and unloading pieces of code (Drivers) hot, without rebooting. These pieces are called **Kernel Modules (.ko)**.

This gives us the best of both worlds: the speed of a monolith and the flexibility of being able to plug in new hardware (a USB, a camera) without having to recompile and restart the entire computer.

@section: 2. Module Management: Hot Mechanics

Most drivers in your Linux system are not "soldered" inside the `vmlinuz` file. They are external files living on the hard drive, typically in `/lib/modules/$(uname -r)/`.

The Kernel loads these files into RAM only when needed. This keeps the Kernel light and fast.

### Exploring Loaded Modules (`lsmod`)
What does your Kernel have loaded right now? Let's see.

```bash
$ lsmod
```

You will see a table with three columns:
1.  **Module:** The module name (e.g., `iwlwifi`, `nouveau`, `ext4`).
2.  **Size:** How much it occupies in memory (in bytes).
3.  **Used by:** A counter and a list.
    *   The number indicates how many processes or other modules are using this module.
    *   The list says *who* they are.

**Example:**
```text
Module                  Size  Used by
iptable_filter         12345  1
ip_tables              23456  1 iptable_filter
nouveau               987654  3
video                  45678  1 nouveau
```
Here we see `video` is being used by `nouveau` (the graphics driver). This means **we cannot unload** the `video` module while `nouveau` is active. There is a dependency.

### Forensic Information (`modinfo`)
You see a module called `e1000e` and don't know what it is. Interrogate it.

```bash
$ modinfo e1000e
```

The output is pure gold:
*   **filename:** Where the `.ko` file is on disk.
*   **description:** "Intel(R) PRO/1000 Network Driver". Ah, it's the network card!
*   **author:** Who wrote it (Intel Corporation).
*   **license:** GPL.
*   **depends:** What other modules it depends on.
*   **parm:** (Parameters). This is the most useful part for tuning. It tells you what options you can pass to the module to change its behavior.
    *   Example: `parm: debug:Debug level (0=none,..., 16=all) (int)`

### Loading Modules (`modprobe` vs `insmod`)
There are two ways to load a module, the dumb way and the smart way.

1.  **The dumb way (`insmod`):**
    `sudo insmod /path/to/module.ko`
    *   It is "dumb" because it doesn't resolve dependencies. If module A needs B, and you load A, `insmod` will fail and tell you "Unknown symbol". Only used in development.

2.  **The smart way (`modprobe`):**
    `sudo modprobe module_name`
    *   It is smart. Checks a dependency database (`modules.dep`). If you ask it to load A, it sees it needs B, so it loads B first and then A. **Always use `modprobe`.**

**Practical example: Load support for Btrfs**
```bash
$ sudo modprobe btrfs
```
If there are no errors, the command says nothing (unix silence). Now your Kernel knows how to speak the Btrfs file system language.

### Unloading Modules (`modprobe -r`)
To remove a driver (for example, if it hung or you want to update it):

```bash
$ sudo modprobe -r btrfs
```
*(`-r` means Remove).*

**Common error:** `modprobe: FATAL: Module btrfs is in use.`
This happens because you try to remove the driver while you have a Btrfs disk mounted. The Kernel protects you from your own stupidity. Unmount the disk first (`umount`) and then unload the module.

@quiz: You are trying to unload a module with `modprobe -r` but get an error saying it is in use. What command would help you identify what other modules or processes are using it?
@option: modinfo
@correct: lsmod
@option: insmod
@option: dmesg

@section: 3. Persistent Configuration and Blacklisting

Changes made with `modprobe` are **volatile**. If you restart the computer, the Kernel returns to its original state and loads only what it detects automatically.

How do we make permanent changes? Using configuration files in `/etc`.

### Load modules at boot (`/etc/modules`)
If you have a module that the system doesn't detect automatically (rare nowadays, but happens with custom hardware), you can force its load.
Edit `/etc/modules` (or create a file in `/etc/modules-load.d/`).
Simply write the module name on a line.

### Pass parameters (`/etc/modprobe.d/`)
Imagine your WiFi card (`iwlwifi`) is unstable and you read on a forum that disabling 11n mode fixes it.
`modinfo iwlwifi` tells you it has a parameter `11n_disable`.

Create a file `/etc/modprobe.d/wifi.conf`:
```text
options iwlwifi 11n_disable=1
```
On the next reboot, the Kernel will apply that option automatically.

### The Blacklist (Blacklisting)
Sometimes, the Kernel loads a driver you do **NOT** want.
Most famous case: You want to install proprietary NVIDIA drivers. But Linux, by default, loads the free driver `nouveau`. Both drivers fight for the graphics card and the system explodes.
You need to tell the Kernel: "Under no circumstances load nouveau".

Create `/etc/modprobe.d/blacklist-nvidia.conf`:
```text
blacklist nouveau
options nouveau modeset=0
```

**CRITICAL STEP! Regenerate Initramfs**
Many modules (like disk or graphics) load *before* the Kernel can read your main hard drive. They load from `initramfs` (the boot backpack).
If you edit `/etc/modprobe.d/`, you only change the hard drive, but the backpack still has the old driver.
You must update the backpack:

*   **Debian/Ubuntu:** `sudo update-initramfs -u`
*   **RedHat/Fedora:** `sudo dracut --force`

@quiz: You just created a file in `/etc/modprobe.d/` to blacklist a video driver causing conflicts during boot. You reboot, but the driver still loads. What step did you forget?
@option: Give execution permissions to the .conf file.
@option: Run command `sysctl -p`.
@correct: Regenerate the initramfs (with `update-initramfs` or `dracut`) so the configuration is included in the initial boot image.
@option: Restart systemd-modules-load service.

@section: 4. Real-time Kernel Tuning (`sysctl`)

The Kernel is not static. It is a living organism with thousands of variables controlling its behavior.
How much memory used for cache? How to handle network packets? Should it reboot if it hangs?

These adjustment "knobs" are exposed through the virtual file system **`/proc`**.
Specifically in **`/proc/sys/`**.

### Exploring the Kernel brain
Go there.
```bash
$ cd /proc/sys
$ ls
abi  debug  dev  fs  kernel  net  vm ...
```
Everything you see looks like files, but they are not on the hard drive. They are direct windows into Kernel memory.

*   **`net/`**: Network configuration (IPv4, IPv6, Core).
*   **`vm/`**: Virtual memory (RAM, Swap, Cache).
*   **`kernel/`**: Core configs (Panic, Hostname, PIDs).
*   **`fs/`**: File system (Open file limits).

If you do `cat /proc/sys/vm/swappiness`, you see a number (e.g., `60`).
If you do `echo 10 > /proc/sys/vm/swappiness` (as root), you changed operating system memory management behavior instantly!

### The `sysctl` Tool
Although `echo` works, the professional and safe way is the `sysctl` command.

**Read a value:**
```bash
$ sysctl vm.swappiness
vm.swappiness = 60
```

**Write a value (Temporary):**
We want the system to use less swap to improve desktop performance.
```bash
$ sudo sysctl -w vm.swappiness=10
```
Change is immediate. No need to restart services. But if you reboot PC, it will be lost.

### Make it Persistent (`/etc/sysctl.conf`)
For changes to survive reboot, write them in `/etc/sysctl.conf` or a file inside `/etc/sysctl.d/`.

**Example hardening configuration:**
```ini
# /etc/sysctl.d/99-security.conf

# Ignore Pings (ICMP Echo Request) - Stealth Mode
net.ipv4.icmp_echo_ignore_all = 1

# Disable packet forwarding (So PC is not used as router)
net.ipv4.ip_forward = 0

# Protection against SYN Flood attacks
net.ipv4.tcp_syncookies = 1

# Disable IPv6 if not using (reduce attack surface)
net.ipv6.conf.all.disable_ipv6 = 1
```

To apply these changes without rebooting:
```bash
$ sudo sysctl -p /etc/sysctl.d/99-security.conf
```

@quiz: You want your Linux server to stop responding to `ping` commands to be less visible on the network. What `sysctl` parameter should you modify?
@option: net.ipv4.ip_forward
@option: vm.swappiness
@correct: net.ipv4.icmp_echo_ignore_all
@option: net.ipv4.tcp_syncookies

@section: 5. Kernel Tainting (The Stain)

Sometimes, looking at system logs (`dmesg` or `journalctl -k`), you see a worrying message at start:
`Kernel tainted`.

Is your system infected? Broken?
Not necessarily.

**GPL Philosophy and Purity:**
Linux Kernel is Free Software (GPL). Kernel developers can only guarantee stability and debug errors if they have access to *all* source code running.
If you load a **Proprietary** (Closed Source) module, like NVIDIA driver (`nvidia.ko`) or some proprietary WiFi drivers, Kernel detects "secret code" entered its memory space.

At that moment, Kernel raises a flag: **"I am Tainted"**.

**Consequences:**
1.  **Functionality:** None. Your system will work perfectly (or better, if you needed that driver to play).
2.  **Support:** If system crashes (Kernel Panic) and you send bug report to Kernel developer community, they see "Tainted" flag. Automatically reject report. They say: *"We can't know if fault is ours or secret NVIDIA driver. Reproduce error without tainted driver and tell us"*.

It is a "Warranty Void" mark for open source developers.

@section: 6. Initiation Rite: Compiling Your Own Kernel

We arrive at the trial by fire. The SysAdmin black belt.
Why compile your own kernel in 2025, when distros give you a perfect one?

**Reasons to compile:**
1.  **Bleeding Edge Hardware:** Laptop so new touchpad driver only in version 6.9, but Ubuntu uses 6.5.
2.  **Extreme Optimization:** Want Kernel for embedded system (IoT) taking 2MB instead 100MB. Remove support for everything except exact hardware.
3.  **Security and Patches:** Need to apply critical security patch (zero-day) released today in source code, but distro takes week to package.
4.  **Learning:** Because you don't understand Linux until you build Linux.

**WARNING:** If you fail this process, system may stop booting. Ensure always have functional old Kernel in GRUB menu to go back. Never delete old kernel until new one works for a week.

### Step 1: Prepare Ground
Need build tools. Libraries processing C code, assembly, certificates.

Debian/Ubuntu:
```bash
$ sudo apt update
$ sudo apt install build-essential libncurses-dev bison flex libssl-dev libelf-dev
```

### Step 2: Get Sources (Code)
Go to **kernel.org**. Official source. See latest stable version (say 6.8.1).
Download "Tarball" (.tar.xz).

```bash
# Work in /usr/src, standard place for sources
$ cd /usr/src
$ sudo wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.8.1.tar.xz
$ sudo tar -xvf linux-6.8.1.tar.xz
$ cd linux-6.8.1
```

### Step 3: Configuration (`.config`)
Kernel has 10,000+ configurable options.
*   Ham radio support?
*   Old Apple Macintosh filesystem?
*   90s joystick drivers?

Trying to configure from scratch takes days and fails.
Smart strategy: **Copy current distro config**.

```bash
$ cp /boot/config-$(uname -r) .config
```
Copies configuration file of current kernel to source folder.

Now, open graphical config menu (text based):
```bash
$ make menuconfig
```
Blue BIOS-style screen appears. Navigate enable/disable features.
*   **`[*]` (Asterisk):** Integrated (Built-in). Code goes inside `vmlinuz` file. Always active. Increases boot size. (Mandatory for hard disk driver and root filesystem).
*   **`[M]` (Module):** Compiled as external `.ko` file. Loads only if needed. (Recommended for almost everything else).
*   **`[ ]` (Empty):** Excluded. Not compiled.

*Tip:* If just want to update kernel without changing options, just exit and save.

### Step 4: Compilation
Moment of truth. Converting millions lines C code to machine code.
Depending on CPU, takes 10 minutes (32-core Threadripper) to 5 hours (Raspberry Pi).

Use flag `-j` to tell `make` how many cores to use. Good rule `nproc` (number of processors).

```bash
$ make -j$(nproc)
```
Screen floods text. Sit back enjoy. Computer working 100%. If ends without "Error", success.

### Step 5: Module Installation
Previous step created `vmlinuz` and thousands `.ko` files. Now copy to place.
First modules:
```bash
$ sudo make modules_install
```
Copies to `/lib/modules/6.8.1/`.

### Step 6: Kernel Installation
Now install core.
```bash
$ sudo make install
```
Command does black magic scripted by distribution:
1.  Copies `vmlinuz` to `/boot`.
2.  Copies `System.map` (symbol table for debug) to `/boot`.
3.  Copies `.config` to `/boot`.
4.  **Auto generates initramfs** for new kernel.
5.  **Updates GRUB** adding new entry automatically.

### Step 7: Reboot
Cross fingers.
```bash
$ sudo reboot
```
In GRUB menu, select "Advanced Options" if not default, or let boot. Should see new version "Linux 6.8.1".

If boots, open terminal type `uname -r`.
If see `6.8.1`... **Congrats! Compiled car engine and driving it.**

@section: 7. DKMS: Update Savior

Compiling whole kernel fun, but what if just want install new WiFi driver not in kernel?
Usually download driver code compile.
But, **problem!**
Driver compiles "against" current kernel version.
If next week `apt upgrade` updates system kernel, WiFi driver stops working because compiled for old kernel. Must recompile manually every update. Hell.

**Solution: DKMS (Dynamic Kernel Module Support)**
DKMS system automates this.
When install driver with DKMS support (like NVIDIA or VirtualBox):
1.  Driver source code saves in `/usr/src`.
2.  DKMS installs "hook" in kernel update system.
3.  When install new Kernel, DKMS detects and **automatically recompiles driver for new kernel** before reboot.

Thanks DKMS, have external drivers update kernel without fear losing graphics WiFi.

@section: Summary / Cheat Sheet

| Command / File | Function |
| :--- | :--- |
| `lsmod` | List currently loaded modules. |
| `modinfo [mod]` | See details, author, parameters of module. |
| `modprobe [mod]` | Load module resolving dependencies. |
| `modprobe -r [mod]` | Unload module. |
| `/etc/modprobe.d/` | Folder for persistent config (options, blacklist). |
| `sysctl -a` | See all kernel parameters real time. |
| `sysctl -w var=val` | Change parameter temporarily. |
| `/etc/sysctl.conf` | Persistent sysctl changes. |
| `make menuconfig` | Graphic menu configure kernel compilation. |
| `uname -r` | See executing kernel version. |

Kernel no longer mystery. Program. Complex, vital, fascinating program, but program you, administrator, control, adjust, rebuild.
