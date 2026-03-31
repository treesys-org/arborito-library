@title: Disaster Recovery (Rescue Mode)
@icon: 🚑
@description: You broke the system. It will not boot. No SSH. Learn chroot, GRUB edits, and recovering lost root passwords.
@order: 3

# Disaster recovery: boot, chroot, and backups

Breaking `/etc/fstab`, losing the root password, or deleting `/boot` happens in the real world. **LPIC-2** expects **rescue mode**, **chroot**, and backup concepts.

@section: 1. GRUB: `init=/bin/bash` and systemd

**Legacy:** add `init=/bin/bash` to the kernel line → root shell; **remount rw** `mount -o remount,rw /` → `passwd`.

**systemd-era options:**

* **`rd.break`** on the kernel cmdline (stops before switch_root — common on RHEL).
* **`systemd.debug-shell`** — debugging only in lab.

**SELinux:** after off-policy edits from rescue, you may need **`touch /.autorelabel`**.

@section: 2. Live USB and full chroot

1. Boot a rescue ISO (same family as the installed system).
2. Find partitions: `lsblk`, `blkid`.
3. Mount root on `/mnt`; if **/boot/efi** is separate, mount it at `/mnt/boot/efi`.
4. Bind mounts:

```bash
for d in dev proc sys run; do mount --bind /$d /mnt/$d; done
```

5. `chroot /mnt` (or `arch-chroot` on Arch).

Inside: `grub2-install`, `update-grub`, `dracut`/`mkinitcpio`, reinstall kernel.

**Btrfs/ZFS:** snapshots for rollback if configured.

@section: 3. Backups: 3-2-1 and tested restores

* **3** copies, **2** media types, **1** off-site.
* **Full image:** `dd`, Clonezilla, hypervisor snapshots.
* **Files:** `rsync`, Borg, Restic.
* **Databases:** logical dumps (`mysqldump`, `pg_dump`) + WAL/binlog per RPO.

**Golden rule:** a backup without a **tested restore** does not count.

@section: 4. Broken initramfs

If the kernel cannot find modules or root:

```bash
dracut -f          # RHEL
update-initramfs -u -k all   # Debian
```

Run from chroot after mounting the broken system.

@section: 5. LUKS

If `/` is **LUKS**, initramfs must unlock the volume at boot. Rescue: `cryptsetup luksOpen` before chroot.

@section: 6. Runbooks

For each critical server: **recovery procedure** stored outside the DC, with partition **UUIDs** and bootloader version.

@section: 7. systemd rescue targets

Beyond `init=/bin/bash` (crude and risky), systemd provides explicit targets:

* **`systemd.unit=rescue.target`:** early root shell with minimal services.
* **`systemd.unit=emergency.target`:** even more minimal maintenance console.

From there you can **remount** `/` read-write, fix `fstab`, regenerate initramfs, or unlock LUKS. Document in your runbook **which key** opens GRUB on your hardware (Esc, Shift, etc.).

@section: 8. When initramfs cannot find root

Symptoms: **“unable to find root”**, drop to **initramfs shell**. Common causes: wrong UUID on the kernel cmdline, missing disk module, or wrong Btrfs snapshot. From the initramfs shell:

* Inspect `blkid` if available, or look under `/dev`.
* Manually mount root and fix `fstab` / `grub.cfg` from **live media** + chroot if needed.

@section: 9. Btrfs and rescue snapshots

If you use **Btrfs** with snapshots (e.g. **snapper**), try **rollback** to a pre-update snapshot from the boot menu or with `btrfs subvolume list` / `btrfs subvolume set-default` before reinstalling. That can be **minutes** vs **hours**.

@section: 10. Extended lab

1. Deliberately break `/etc/fstab` in a VM (comment `/` or use a fake UUID) and recover with live ISO + chroot; time the RTO.
2. Document measured RTO and what you would do differently with a same-day backup of `/etc`.
3. (Optional) Boot with `rescue.target` and practice `mount -o remount,rw /` on the real root.
4. Write a **post-restore checklist**: network, DNS, critical services, disk space.

@quiz: After mounting the broken root on `/mnt`, what changes the effective root to that tree?
@option: pivot_root
@correct: chroot /mnt (or systemd-nspawn in modern flows)
@option: mount --move

@quiz: What practice proves backups are useful?
@option: Ten copies on one disk
@correct: Periodic restore drills in a test environment
@option: Always gzip

@quiz: On SELinux systems, what may you create at the root of the filesystem after rescue edits to force relabeling?
@option: /etc/hosts
@correct: /.autorelabel (on next boot)
@option: /boot/grub2/grub.cfg
