
@title: Advanced Storage: LVM and RAID
@icon: 💽
@description: The master guide (+1000 lines) to mastering storage in Linux. Leave static partitions behind and learn to manage space like a fluid. Software RAID (mdadm), Logical Volumes (LVM), Snapshots, Thin Provisioning, and disaster recovery.
@order: 3

# The Data Architect: Enterprise Storage Management

Welcome to the module separating amateurs from professionals.

In Junior level, you learned installing Linux on hard drive. Created partition `/`, maybe `/home` `swap`. Used `fdisk` `parted`. Fine personal laptop test VM.

Real world, data center, **storage living organism**.

Imagine situations, everyday SysAdmin life:

1.  **Unexpected Growth:** Database server 500GB disk. Supposed last 3 years. 6 months Marketing campaign success, disk full. Server stops. Boss looks. What do? Shutdown, clone 1TB pray boot? Can't shutdown?
2.  **Physical Failure:** Critical file server. Tuesday 3 AM mechanical disk dies. "Click-click-click" gone. Lost company data? Restore backup yesterday lose today work?
3.  **Hot Migration:** Old server slow disks. Bought new SSDs. Want move OS data SSDs **while** users working, unnoticed.

Traditional partitions (static), logistic nightmares.
With **LVM (Logical Volume Manager)** and **RAID (Redundant Array of Independent Disks)**, problems solved few commands, often no reboot.

Massive guide, become storage architect. Combine physical disks act one, create safety mirrors, expand file systems hot, travel time snapshots.

@section: 1. RAID: Disk Fortress

Start physical base. Hard drives (SSDs) inherently unreliable. Mechanical parts memory cells degrade. **Will fail**. Not "if", "when".

**RAID** (Redundant Array of Independent Disks) technology allows grouping physical disks OS sees single logical unit.

Depending grouping ("Level" RAID), get:
*   **Redundancy:** Disk dies, no data loss.
*   **Speed:** Read/Write multiple disks simultaneous.
*   **Capacity:** Sum size disks.

### Hardware RAID vs. Software RAID
Old expensive servers used physical RAID controller cards. Expensive, grave defect: card breaks, need *identical card* (model firmware) read data. Manufacturer closed, dead.

Modern Linux, use **Software RAID** tool `mdadm` (Multiple Device Admin).
*   **Advantage 1:** Hardware agnostic. Take disks HP server, plug cheap clone PC, Linux recognizes RAID mounts data.
*   **Advantage 2:** Free, extremely powerful fast modern CPUs.

### RAID Levels Explained (Vital Theory)

Before typing, know architecture building.

#### RAID 0 (Stripe)
*   **Concept:** Info divided chunks written alternatively disks.
*   **Req:** Min 2 disks.
*   **Speed:** Extreme. (Speed 1 disk * N disks).
*   **Capacity:** Sum total (100%).
*   **Redundancy:** **ZERO**.
*   **Danger:** **ONE** disk fails, lose **ALL** array data. Statistically double insecure single disk.
*   **Use:** Temp caches, video render original data elsewhere. Never important storage.

#### RAID 1 (Mirror)
*   **Concept:** Everything written Disk A, copied identically Disk B.
*   **Req:** Min 2 disks.
*   **Speed:** Write normal (slowish), Read fast (read both simultaneously).
*   **Capacity:** 50%. (Two 1TB disks give 1TB useful).
*   **Redundancy:** High. One disk fails, system works.
*   **Use:** OS (/boot, /), small critical DBs.

#### RAID 5 (Distributed Parity)
*   **Concept:** Data written multiple disks, calculate "Parity" block (math sum). Parity distributed all disks.
*   **Req:** Min 3 disks.
*   **Capacity:** (N-1) disks. (3 disks 1TB = 2TB useful).
*   **Redundancy:** 1 disk fail anywhere. Data reconstructed math using others parity.
*   **Use:** General file storage.
*   **Disadvantage:** Write slow (calc parity). Disk fail, performance drops until replace "rebuild" (takes long stress remaining disks).

#### RAID 6 (Double Parity)
*   **Concept:** Like RAID 5, two distinct parity blocks.
*   **Req:** Min 4 disks.
*   **Redundancy:** **2 disks** fail simultaneous.
*   **Use:** Large capacity files security vital.

#### RAID 10 (1+0 - Stripe of Mirrors)
*   **Concept:** Hybrid. Make pairs mirrors (RAID 1) join pairs Stripe (RAID 0).
*   **Req:** Min 4 disks.
*   **Capacity:** 50%.
*   **Speed:** Very high (like RAID 0).
*   **Redundancy:** Very high (like RAID 1).
*   **Use:** Gold standard high performance DBs virtualization. Expensive, best.

@quiz: Server 4 disks 2TB each. Maximize read/write speed temp cache video processing, data loss ok re-downloadable. Level?
@option: RAID 1
@option: RAID 5
@correct: RAID 0
@option: RAID 6

@section: 2. RAID Lab: Hands on `mdadm`

Simulate admins. No real physical disks need practice; use files virtual disks VM.
Assume two empty disks available: `/dev/sdb` `/dev/sdc`.

**Step 0: Install**
Debian/Ubuntu:
```bash
$ sudo apt update
$ sudo apt install mdadm
```

**Step 1: Prepare disks**
Can RAID "raw" disks, good practice partition mark "Linux RAID autodetect".
```bash
$ sudo fdisk /dev/sdb
# (Create new 'n', primary 'p', whole disk)
# (Change type 't' to 'fd' - Linux RAID auto)
# (Save 'w')
```
Repeat `/dev/sdc`. Now `/dev/sdb1` `/dev/sdc1`.

**Step 2: Create Array (RAID 1)**
Create virtual device `/dev/md0` (Multi-Disk 0) mirror two disks.

```bash
$ sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
```
Ask sure. `yes`.
*   `--create`: New array.
*   `--level=1`: Type RAID 1 (Mirror).
*   `--raid-devices=2`: How many active disks form array.

**Step 3: Verify state**
Immediately, kernel starts sync disks (copy zeros one other ensure identical).
See process sacred file `/proc/mdstat`:

```bash
$ cat /proc/mdstat
Personalities : [raid1]
md0 : active raid1 sdc1[1] sdb1[0]
      1047552 blocks super 1.2 [2/2] [UU]
      [=>...................]  resync =  5.7% (60000/1047552) finish=1.5min speed=10000K/sec
```
*   `[2/2]`: Expect 2 disks have 2.
*   `[UU]`: Both disks **U**p. If `[_U]`, one broken lost.
*   `resync`: Progress bar.

Also use:
```bash
$ sudo mdadm --detail /dev/md0
```
Detailed forensic report array.

**Step 4: Create Filesystem**
Now `/dev/md0` behaves exactly normal disk.
```bash
$ sudo mkfs.ext4 /dev/md0
```

**Step 5: Mount Use**
```bash
$ sudo mkdir /mnt/secure_data
$ sudo mount /dev/md0 /mnt/secure_data
```
Ready! Everything saved `/mnt/secure_data` writing two physical disks simultaneously.

**Step 6: Persistence (Crucial)**
Reboot now, RAID might not start change name `/dev/md127`. Save config.
```bash
$ sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
```
Then, vital, update initramfs (initial boot memory) know RAID:
```bash
$ sudo update-initramfs -u
```

@section: 3. Disaster Simulation Recovery

RAID useless know fix broken. Simulate fail.

**Scenario:** Disk `/dev/sdb1` died.

**1. Mark disk failed:**
Virtual disks don't break physically, tell kernel pretend broken.
```bash
$ sudo mdadm /dev/md0 --fail /dev/sdb1
```
Check `/proc/mdstat`, see `[2/1] [_U]`. System degraded, **still working**. Data accessible thanks other disk.

**2. Remove dead disk:**
Tell RAID forget disk remove.
```bash
$ sudo mdadm /dev/md0 --remove /dev/sdb1
```

**3. Physical Replacement:**
(Shutdown, remove bad, insert new, power on. Or hot swap if server supports).
Suppose new disk recognized `/dev/sdd`.
Partition same other (`fdisk`). Say `/dev/sdd1`.

**4. Add new disk Array:**
```bash
$ sudo mdadm /dev/md0 --add /dev/sdd1
```

**5. Rebuild:**
Automatically, RAID sees new fresh disk starts cloning survivor data new.
Watch `/proc/mdstat` magic recovery live.
Finish, `[UU]` back. System healthy.

@quiz: RAID 1 config two disks. One fails. What happens data service?
@option: Service stops data lost replace disk.
@correct: Service continues working interruption no data loss, array degraded (no redundancy).
@option: System powers off safety.
@option: RAID converts RAID 0.

@section: 4. LVM: Logical Volume Manager

Protected disks RAID. Now manage space **LVM**.
LVM abstraction layer *above* physical disks (RAID) *below* file system.

### LVM Vocabulary
Understand LVM, think LEGO.

1.  **PV (Physical Volume):** Base brick. Hard disk (`/dev/sda`), partition (`/dev/sdb1`), better, RAID device (`/dev/md0`). LVM "marks" devices use.
2.  **VG (Volume Group):** "Pool" pile LEGO pieces. Group one more PVs VG.
    *   Example: Disk 1TB Disk 2TB. Create VG `server_vg`. Now storage pool 3TB. System don't care which disk which. Sees "3TB free".
3.  **LV (Logical Volume):** Built pieces. Virtual partition.
    *   Take 50GB pool create `lv_root`.
    *   Take 500GB `lv_home`.
    *   Magic LVs elastic. `lv_root` full? Space left pool (VG), enlarge LV hot.

### Mental Diagram
`Physical Disk (PV)` -> `Group (VG)` -> `Logical Volume (LV)` -> `Filesystem (ext4/xfs)`

@section: 5. LVM Lab: Creation Expansion

Create LVM system scratch simulate no space.
Use RAID `/dev/md0` base (PV), robust flexible system.

### Phase 1: Creation

**1. Init Physical Volume (PV):**
"Mark" RAID LVM use.
```bash
$ sudo pvcreate /dev/md0
Physical volume "/dev/md0" successfully created.
```

**2. Create Volume Group (VG):**
Call group `vg_data`.
```bash
$ sudo vgcreate vg_data /dev/md0
Volume group "vg_data" successfully created
```
Verify `sudo vgs`. See VG total RAID size free.

**3. Create Logical Volumes (LV):**
Create two volumes: projects backups.
```bash
$ sudo lvcreate -L 10G -n lv_projects vg_data
$ sudo lvcreate -L 20G -n lv_backups vg_data
```
*   `-L 10G`: Fixed size 10 Gigabytes.
*   `-n name`: Volume name.
*   `vg_data`: Group space source.

Now exist two new devices `/dev/mapper/`:
*   `/dev/mapper/vg_data-lv_projects`
*   `/dev/mapper/vg_data-lv_backups`

**4. Format Mount:**
Same always.
```bash
$ sudo mkfs.ext4 /dev/mapper/vg_data-lv_projects
$ sudo mkfs.ext4 /dev/mapper/vg_data-lv_backups
$ sudo mkdir /mnt/projects /mnt/backups
$ sudo mount /dev/mapper/vg_data-lv_projects /mnt/projects
$ sudo mount /dev/mapper/vg_data-lv_backups /mnt/backups
```

### Phase 2: Expansion ("Disk Full" scenario)

Alarm. Dev team says `/mnt/projects` (10GB) full. Need 5GB more. Space free group `vg_data`.
How do without shutdown unmount?

**1. Extend Logical Volume (LV):**
Tell LVM add 5GB container.
```bash
$ sudo lvextend -L +5G /dev/vg_data/lv_projects
```
Message says logical volume changed 10GB 15GB.
**WARNING!** `df -h`, still 10GB.
Why?
Enlarged "room", not "carpet" (ext4 filesystem). Filesystem doesn't know disk below grew.

**2. Resize Filesystem:**
Tell ext4 expand occupy new space.
```bash
$ sudo resize2fs /dev/vg_data/lv_projects
```
*(Note: XFS filesystem, command `xfs_growfs /mnt/projects`).*

Done! `df -h`. 15GB available. Server stopped zero seconds.

### Phase 3: Add more physical disks

Volume Group full (pool)?
Imagine `vg_data` full. Buy new disk `/dev/sdd`.

1.  Create PV: `sudo pvcreate /dev/sdd`.
2.  Extend VG: `sudo vgextend vg_data /dev/sdd`.
    *   Pool bigger!
3.  Extend LVs before.

Capacity add arbitrary disks group makes LVM powerful.

@quiz: Executed `lvextend` add 10GB logical volume, `df -h` shows old size. Missing step?
@option: Reboot server.
@option: Remount partition.
@correct: Resize filesystem (with `resize2fs` ext4 `xfs_growfs` XFS).
@option: Execute `pvcreate`.

@section: 6. LVM Snapshots: Time Machine

Powerful LVM function **Snapshot**.
Snapshot not backup complete (no duplicate data). "Photo" disk state moment using **Copy-on-Write (CoW)**.

*   Create snapshot. Initially 0 space.
*   LVM watches.
*   Something tries modify data block original disk, LVM copies *original* block (before change) saves snapshot area.
*   Snapshot grows change original.

**Practical Use: Dangerous Updates**
Update server fear break.

1.  **Create Snapshot:**
    ```bash
    $ sudo lvcreate -L 2G -s -n snap_before_update /dev/vg_data/lv_projects
    ```
    *   `-s`: Snapshot.
    *   `-L 2G`: Reserve 2GB save changes (deltas). Change more 2GB original, snapshot breaks.

2.  **Make changes:**
    Install updates, delete files, break `/mnt/projects`.

3.  **Check disaster:**
    Broken. Return back.

4.  **Restore (Merge):**
    Restore, unmount original.
    ```bash
    $ sudo umount /mnt/projects
    $ sudo lvconvert --merge /dev/vg_data/snap_before_update
    ```
    LVM takes original blocks saved snapshot puts back. Snapshot disappears (auto-consumed).

5.  **Mount again:**
    `sudo mount /dev/mapper/vg_data-lv_projects /mnt/projects`.
    Exactly before start!

@section: 7. Thin Provisioning: Abundance Illusion

Virtualization environments (create VPS clients), want "oversell" space.
1TB real. Create 20 VMs tell each 100GB disk.
Total promised: 20 * 100 = 2TB.
Total real: 1TB.

Called **Thin Provisioning**.
Create "Thin Pool". Volumes inside occupy space promised. Only occupy data *really* written.
User thinks 100GB saved 2GB photos, physical disk 2GB used.

**Commands:**
```bash
# Create Thin Pool
$ sudo lvcreate -L 500G -T vg_data/thinpool

# Create Thin volume inside
$ sudo lvcreate -V 100G -T vg_data/thinpool -n vm_client1
```
Client sees 100GB. You spend 0.

**Risk:** Clients decide fill disks simultaneous exceed 1TB physical, system crashes. Monitor closely.

@section: 8. Partitioning Strategy Recommended Servers

Mount professional server, winning scheme:

1.  **Physical Disks:** 2 identical disks (e.g., `/dev/sda`, `/dev/sdb`).
2.  **Boot Partition (EFI/Boot):** Outside LVM/RAID (or simple RAID 1 metadata old). Usually small 512MB start each disk.
3.  **RAID:** Create RAID 1 (`/dev/md0`) rest space both disks.
4.  **LVM:**
    *   PV: `/dev/md0`.
    *   VG: `system_vg`.
    *   LV `root`: 20GB `/`.
    *   LV `swap`: 4GB (needed).
    *   LV `var`: 20GB `/var` (logs, docker, DBs).
    *   LV `home`: Rest (leave free assign later).

Config redundancy (disk fail, alive) flexibility (expand `/var` logs full).

@quiz: Config critical server want use Software RAID 5 `mdadm`. 3 disks 1TB. Useful space data approx?
@option: 1TB
@correct: 2TB (N-1 disks)
@option: 3TB
@option: 1.5TB

@section: Summary / Cheat Sheet

**RAID (`mdadm`):**
*   Create: `mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1`
*   Status: `cat /proc/mdstat`
*   Detail: `mdadm --detail /dev/md0`
*   Fail disk: `mdadm /dev/md0 --fail /dev/sdb1`
*   Remove disk: `mdadm /dev/md0 --remove /dev/sdb1`
*   Add disk: `mdadm /dev/md0 --add /dev/sdb1`

**LVM:**
*   PV (Physical): `pvcreate`, `pvs`, `pvdisplay`.
*   VG (Group): `vgcreate`, `vgs`, `vgextend`.
*   LV (Logical): `lvcreate`, `lvs`, `lvextend`, `lvresize`.
*   Snapshot: `lvcreate -s`.
*   Filesystem: `resize2fs` (ext4), `xfs_growfs` (xfs).

Owner disks, not slave. Adapt storage business needs real time.
