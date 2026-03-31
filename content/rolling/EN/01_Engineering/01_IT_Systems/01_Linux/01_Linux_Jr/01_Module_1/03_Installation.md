@title: Disks and Files: The Ultimate Guide
@icon: 💾
@description: The "Bible" guide to understanding why C: doesn't exist, what mounting is, how to partition, and how to survive in the Linux directory tree.
@order: 3

# The Treasure Map: Mastering Storage in Linux

> **WARNING:** This is a long chapter. Very long. But it is necessary. If you come from Windows, your brain is wired to think in "Drives" (C:, D:). Linux works in a fundamentally different way. If you don't understand this, you will feel lost forever. If you understand it, you will see that the Windows way is the strange one.

@section: 1. The Trauma of the C Drive:

Let's start with what scares newcomers the most. You plug in your USB drive. You open the file explorer. You look for drive `E:` or `F:`.
**It doesn't exist.**
You look for `My Computer`.
**It doesn't exist.**

In Windows, storage is based on the concept of **Islands**.
*   Your main hard drive is Island `C:`.
*   Your second drive is Island `D:`.
*   Your USB is Island `E:`.
These islands do not touch. They are separate worlds.

In Linux (and in UNIX, and in macOS), storage is based on the concept of the **Single Tree**.
*   There is only one root: **`/`** (The Root).
*   **Everything** hangs from there. Absolutely everything.
*   If you connect a new hard drive, you don't create a new island. What you do is "paste" or "graft" that drive onto a branch of the existing tree.

### The Mansion Analogy
Imagine your computer is a **Giant Mansion**.
*   **Windows:** Every time you buy a new room (a hard drive), you build a separate little house in the garden and give it a letter (C, D, E). To go from one to another, you have to go out to the street.
*   **Linux:** The mansion has a main entrance (`/`). When you buy a new room (a hard drive), you open a door in the hallway and paste the room there. From the inside, it looks like the mansion has simply grown. There are no seams. Everything is under the same roof.

This is called **The Unified Filesystem**.

@quiz: In Linux philosophy, how is a second hard drive connected to the system represented?
@option: As a separate drive with a letter (e.g., D:).
@correct: As just another directory within the main tree (e.g., /mnt/disk2).
@option: A second drive cannot be connected.

@section: 2. The Mansion Map (FHS)

So you don't get lost in this infinite mansion, there is a standardized architectural blueprint called **FHS (Filesystem Hierarchy Standard)**. It is not a physical law: it is a **standard** maintained by the Linux Foundation (see FHS 3.0, 2015). Distributions follow it closely so admins, packages, and certifications (e.g. **LPIC-1**, topic 104.1) share one mental map.

**Why care?** When someone says “check `/etc`” or “the log is under `/var/log`”, it is not random: it is the shared layout. Knowing FHS saves hours and lets you read Debian, Fedora, or RHEL docs without re-mapping paths in your head.

### A little history: the `/usr` merge

On older UNIX systems, `/bin` vs `/usr/bin` mattered more: minimal boot tools on `/`, most everything else under `/usr` (sometimes another disk). On **modern Linux**, many distros use **usr merge**: `/bin`, `/sbin`, and `/lib` are **symbolic links** into `/usr/bin`, `/usr/sbin`, and `/usr/lib`. One coherent tree; easier packaging. If `ls -l /bin` shows `bin -> usr/bin`, you are seeing that design. The FHS concepts still apply; only the **physical** location changes.

### `/` (The root of the tree)

The top of the namespace. Only the superuser should write here casually; normal users work mainly under `/home`. Paths starting with `/` are **absolute** (`/etc/hosts`); others are **relative** to the current directory.

**Do not confuse:** the filesystem root is `/`. The superuser account is often called root. That account’s home directory is `/root`. Three different ideas.

### `/boot` (Boot files)

Kernel images (`vmlinuz-*`), **initramfs** (early userland with minimal drivers), and on UEFI systems the EFI partition is often mounted at `/boot/efi`. If `/boot` is lost or corrupted, the machine may not boot even if `/` is fine. Kernel updates install new files here and refresh the bootloader (GRUB).

### `/bin` and `/usr/bin` (User binaries)

“Binary” = **executable**. When you type `ls`, `cp`, `cat`, or `mkdir`, the shell resolves them via `PATH` (usually including `/usr/bin` and `/bin`).

*   **`/bin`:** in theory, the bare minimum for rescue; on merged systems it often points to `/usr/bin`.
*   **`/usr/bin`:** most user commands and many non-GUI utilities.

### `/sbin` and `/usr/sbin` (System binaries)

Administration tools: `fdisk`, `ip`, `iptables`/`nft`, often `systemctl`, etc. “sbin” does not mean “only root can run” (some tools allow controlled access), but these are **not** casual user toys.

### `/etc` (Host-specific configuration)

**The sysadmin’s main room.** Most configuration is **plain text** (sometimes YAML/JSON, still readable).

*   Examples: `/etc/passwd`, `/etc/group`, `/etc/hosts`, `/etc/fstab`, `/etc/ssh/sshd_config`.
*   Many services use **`*.d` drop-in directories**: snippets included in order (`conf.d`, `sudoers.d`) so packages add files without overwriting yours.

**Golden rule:** copy before editing: `sudo cp file file.bak`. Track what you changed.

### `/home` (User home directories)

Linux is **multi-user**. Each login has `/home/username` (with special cases). Documents, projects, and **dotfiles** (`.bashrc`, `.config`, `.ssh`) live here. Default permissions are usually restrictive so other users cannot read your data.

### `/root` (Superuser home)

Not under `/home` so it remains reachable if `/home` is on a broken separate filesystem or unmounted during rescue.

### `/var` (Variable data)

Data that **changes** at runtime: logs, mail queues, package caches, application data under `/var/lib`, web roots like `/var/www` on many installs, print spool, etc. Filling the disk with logs or databases is a classic ops failure—monitor `/var` (sometimes on its own partition).

### `/tmp` and `/var/tmp` (Temporary data)

*   **`/tmp`:** world-writable with sticky bit; short-lived files. Often **tmpfs** (RAM) and **cleared on reboot**.
*   **`/var/tmp`:** temporaries that should **survive** reboots (depending on policy).

Never store secrets or critical data in temp areas.

### `/dev` (Device nodes)

**“Everything is a file.”** Disks (`/dev/sda`, partitions `sda1`), terminals (`/dev/tty*`), randomness (`/dev/urandom`), the sink (`/dev/null`). `ls -l` shows major/minor numbers that identify the kernel driver.

### `/proc` and `/sys` (Kernel interfaces)

Not real disks: **virtual filesystems**. `/proc` exposes processes (`/proc/<pid>/`), `cpuinfo`, `meminfo`, tunables via `sysctl`. `/sys` describes devices and hardware in a tree. Tools like `lscpu` or `free` are reading these files for you.

### `/run` (Runtime state)

Replaces much of what used to live under `/var/run`. **PID files**, daemon sockets, **ephemeral** state that must not survive reboot. Often `/var/run` is a symlink to `/run`.

### `/srv` (Service data)

Convention for data **served** by a service (FTP, internal repos, etc.). Not every distro uses it; many still use `/var/www`. What matters is **documenting** where *your* service stores data.

### `/mnt` and `/media` (Mount points)

*   **`/media`:** automatic mounts (USB, external disks) by the desktop stack.
*   **`/mnt`:** administrators create subdirs for **manual** mounts (`/mnt/backup`).

### `/lib`, `/lib64`, and friends (Libraries)

Shared libraries (like Windows `.dll`) and modules binaries need. On 64-bit systems you may see `lib64` or `/usr/lib/x86_64-linux-gnu` (Debian/Ubuntu). **Kernel modules** usually live under `/lib/modules/<kernel-version>/`.

### `/usr` (Secondary hierarchy)

Most read-only operating system programs and data: `/usr/share` (docs, icons, time zones), `/usr/include` (headers), `/usr/src` (sources). In a typical install, almost everything that is not mutable host config lives here or under `/var`.

### `/usr/local` (Locally installed software)

Software you **build or install manually** on that machine (not from the distro’s package manager) usually goes under `/usr/local/bin`, `/usr/local/lib`, etc. It stays separate from distributor packages. On servers with custom builds, this tree is sacred.

### `/opt` (Add-on application packages)

Third parties install self-contained trees (`/opt/google/chrome`-style). “Optional” means the base OS can exist without it.

### `lost+found` (Filesystem repair)

At the root of each **ext** filesystem you see this directory. `fsck` places recovered fragments there after a repair. Hopefully you never need to look inside.

### Mental exam table (LPIC-104.1 snapshot)

| Path | One-line memory hook |
| :--- | :--- |
| `/etc` | System configuration |
| `/var` | Growing/changing data (logs, app state) |
| `/home` | User accounts |
| `/tmp`, `/var/tmp` | Temporary files |
| `/usr` | Standard read-only OS programs and data |
| `/usr/local` | Admin-installed local software |
| `/opt` | Large third-party bundles |
| `/boot` | Kernel and initramfs |
| `/dev` | Device nodes |
| `/proc`, `/sys` | Kernel interfaces |

### Quick lab (15 minutes)

1.  `ls -l /` — spot symlinks (`bin -> usr/bin`, etc.).
2.  `man hier` — hierarchy overview (on some systems `man file-hierarchy` instead).
3.  `readlink -f /bin/sh` — which shell your system actually uses by default.
4.  `cat /proc/cpuinfo | head` and `cat /proc/meminfo | head` — read the kernel directly.
5.  `df -h` and `lsblk` — connect mount points to the mental map.

@quiz: In which directory would you look for system configuration files (such as network or user configuration)?
@option: /bin
@option: /home
@correct: /etc
@option: /var

@section: 3. The Concept of "Mounting"

This is what confuses Windows users the most. Let's explain it slowly.

You have a USB drive full of photos.
1.  You plug it into the USB port.
2.  The Kernel (the core) detects it. It says: *"I have found a mass storage device. I will call it `/dev/sdb`"*.
3.  But `/dev/sdb` is just a raw device file. You can't enter it. You can't see the photos.

To use it, you have to **Mount** it.
Mounting means: *"Take the file system inside this device and make it accessible in this folder of my tree."*

**The Manual Process (what the system does for you):**
1.  You create an empty folder where you want the photos to appear:
    `mkdir /mnt/my_photos`
2.  You give the mount command:
    `mount /dev/sdb1 /mnt/my_photos`
    *(Note: sdb1 is the first partition of disk sdb)*.
3.  Magic! Now, if you enter `/mnt/my_photos`, you will see the contents of the USB. That folder is now a window to the device.

**Unmount:**
Before removing the USB, you have to tell the system to stop using it so it finishes writing pending data.
`umount /mnt/my_photos`
(Note that it is `umount`, without the 'n' after the 'u').

@quiz: What command is used to safely disconnect a mounted device before physically removing it?
@option: disconnect
@option: unmount
@correct: umount
@option: eject

@section: 4. Filesystems: The Language of the Disk

A hard drive, physically, is like a giant blank notebook. To write in it, you need to draw lines, margins, and page numbers. You need rules about how to save information. Those rules are the **Filesystem**.

Windows and Linux speak different languages.

### Windows Languages
*   **FAT32:** The universal language. Old, simple, compatible with everything (Windows, Mac, Linux, consoles, TVs).
    *   *Disadvantage:* Does not support files larger than 4GB. Forget saving 4K movies.
*   **NTFS:** The modern Windows standard.
    *   *Linux:* Linux can read and write it perfectly today, but it cannot be installed *on* it. Linux cannot "live" on an NTFS partition because NTFS does not understand Linux user permissions.

### Linux Languages
To install Linux, you need to format the disk in a language that Linux understands natively.

#### 1. Ext4 (Fourth Extended Filesystem)
The gold standard. The "Toyota Corolla" of disks.
*   **Pros:** Solid as a rock. Very tested. It is almost impossible to lose data due to the file system.
*   **Journaling:** This is its star feature. Before writing a file to disk, Ext4 writes a note in a special "journal": *"I am going to write file photo.jpg in sector 500"*.
    *   If the power goes out in the middle of writing, upon restart, the system reads the journal, sees that the operation was half done, and fixes it (either finishes it or undoes it). Without this, your disk would be corrupt.
*   **Usage:** It is default in Ubuntu, Mint, Debian. Use it if you are new.

#### 2. Btrfs (B-Tree Filesystem)
The "Next-Gen" system. It is much more advanced.
*   **Copy-on-Write (CoW):** Never overwrites data. If you modify a file, it writes the changes to a free space and then updates the pointer. This prevents corruption.
*   **Snapshots:** You can take a "photo" of your entire disk in 1 second. Takes up almost 0 space.
    *   *Use case:* Before updating the system, take a snapshot. If the update breaks your Linux, restart, select the snapshot from 5 minutes ago, and the system returns *exactly* to how it was. It's like magic.
*   **Usage:** Default in Fedora and openSUSE.

#### 3. XFS
The enterprise giant.
*   **Pros:** Incredibly fast handling gigantic files and massive databases. Used on NASA and Facebook servers.
*   **Cons:** More complex to reduce in size.
*   **Usage:** Red Hat Enterprise Linux servers.

### Fragmentation?
In Windows, you had to "Defragment the disk" because files were split into scattered chunks.
Linux systems (Ext4, XFS) are much smarter. When they write a file, they look for a space large enough to fit it whole, or leave extra space around it to grow.
**In Linux, you DO NOT need to defragment.** In fact, doing so on an SSD is harmful.

@section: 5. Partitioning: Dividing the Cake

When you install Linux, you have to decide how to divide your hard drive (Pizza). Each slice is a **Partition**.

### MBR vs. GPT
Before partitioning, the disk needs a "Partition Table" (the book's index).
*   **MBR (Master Boot Record):** The old system (80s). Only allows 4 primary partitions and disks up to 2TB. It is obsolete.
*   **GPT (GUID Partition Table):** The modern standard. Allows unlimited partitions and Zettabyte-sized disks. Mandatory for booting in UEFI mode.
**Tip:** Always use GPT unless your computer is from 2008.

### Recommended Partitioning Scheme (2025)
If you are going to install Linux on a modern PC (UEFI), this is the professional and safe scheme:

#### 1. EFI Partition (`/boot/efi`)
*   **Size:** 300 MB - 512 MB.
*   **Format:** FAT32 (Mandatory).
*   **Function:** It is the only partition that the motherboard (BIOS/UEFI) knows how to read upon power up. Here lives the **Bootloader** (GRUB), the program that lets you choose whether to boot Windows or Linux.

#### 2. Root Partition (`/`)
*   **Size:** Minimum 30 GB. Recommended 50 GB - 100 GB.
*   **Format:** Ext4 (or Btrfs).
*   **Function:** Here the Operating System and all programs are installed. It is the equivalent of `C:\Windows` + `C:\Program Files`.

#### 3. Home Partition (`/home`) - HIGHLY RECOMMENDED!
*   **Size:** All remaining space.
*   **Format:** Ext4.
*   **Function:** Here go your personal data (Documents, Photos, Videos, User Configuration).
*   **Why separate it?** This is the great advantage of Linux. If a new version of Ubuntu comes out tomorrow, or you want to switch to Fedora, you can **format the Root partition (`/`)** to install the new system, but **leave the `/home` partition intact**.
    *   When installation finishes, all your files and settings will still be there! You don't have to copy data to an external drive. It is data immortality.

#### 4. Swap (Swap Area)
Swap is "Virtual Memory." If your RAM fills up (you open 50 Chrome tabs), Linux moves the oldest things from RAM to Swap so the PC doesn't freeze.
*   **Formerly:** A separate partition was made.
*   **Modernly (Swapfile):** A giant **file** inside the root partition (`/swapfile`) is used. It is better because you can easily change its size.
    *   Ubuntu and Mint use Swapfile by default. You don't need to create a Swap partition.

### Installation Plan Summary:
1.  **EFI:** 500 MB (FAT32).
2.  **Root (/):** 100 GB (Ext4).
3.  **Home (/home):** Rest of disk (Ext4).

@quiz: What is the main advantage of having `/home` on a separate partition?
@option: Makes the system boot faster.
@correct: Allows reinstalling or changing the operating system by formatting `/` without losing personal files or configurations.
@option: Saves hard drive space.

@section: 6. Commands to Explore Disks

Now that you know the theory, here are the commands to see this in your terminal.

### `lsblk` (List Block Devices)
Shows all disks and partitions in a tree form. It is the most useful command to see what you have connected.
```bash
$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 238.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
└─sda3   8:3    0 188.0G  0 part /home
```
Here you see clearly:
*   `sda`: The physical disk.
*   `sda1`: EFI Partition.
*   `sda2`: Root Partition (System).
*   `sda3`: Home Partition (Data).

### `df -h` (Disk Free)
Shows free and used space of mounted disks. The `-h` is for "Human Readable" (GB instead of bytes).
```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        50G   15G   32G  30% /
/dev/sda3       188G  100G   88G  53% /home
```

### `du -sh` (Disk Usage)
Calculates the size of a folder.
*   `-s`: Summary (Summary, don't list every file).
*   `-h`: Human Readable.
```bash
# How much does my Documents folder take up?
$ du -sh ~/Documents
1.5G    /home/john/Documents
```

@section: 7. Basic Disk Permissions

One last critical thing. Unlike Windows (home versions), Linux is strict about who can see what disk.

If you mount a disk as `root` (the system does it at boot), sometimes a normal user cannot write to it unless permissions are given.
The `/etc/fstab` (Filesystem Table) file is the "master map" that tells Linux which disks to mount at boot and with what permissions.
**DO NOT TOUCH `/etc/fstab` if you don't know what you are doing!** An error here can make the system fail to boot.

@quiz: You want to see a quick visual list of your disks, partitions, and where they are mounted. What command do you use?
@option: df -h
@correct: lsblk
@option: du -sh
@option: fdisk

@section: Conclusion

You have just learned the foundations on which all Linux is built.
1.  There is no C:, there is a Tree (`/`).
2.  Your files are in `/home`.
3.  Configuration is in `/etc`.
4.  External devices are "mounted."
5.  Ext4 is your reliable friend.
6.  Separating `/home` is wise.

Welcome to the architecture of the most powerful operating system in the world!