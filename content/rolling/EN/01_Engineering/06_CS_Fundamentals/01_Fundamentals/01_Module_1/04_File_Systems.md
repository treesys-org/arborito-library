@title: File Systems: Inodes, Journaling, and Consistency
@icon: 📁
@description: ext4, copy-on-write (ZFS/Btrfs), permissions, and links.
@order: 4

# File systems: metadata, consistency, and performance

A **file system** organizes disk blocks into files and directories. **inodes** store metadata; directories map names → inode. **Journaling** (ext4) or **copy-on-write** (Btrfs, ZFS) recover from power loss. This lesson covers Unix **permissions**, **ACLs**, **symlinks** vs **hardlinks**, and the kernel **VFS**.

@section: Structures

**Superblock**, free block **bitmaps**, **inode tables**. **Extents** group contiguous blocks for large files.

@section: Journaling

Transaction writes before applying changes to the main FS; modes **ordered**, **journal**, **writeback** trade safety vs speed.

@section: COW

**Btrfs/ZFS** never overwrite in place: cheap snapshots.

@section: Permissions

`rwx` for user/group/other; **umask** defines creation mask. **setuid** is a security risk.

@section: Suggested lab

1. Inspect `stat`, `ls -i`, `df -T` on Linux.
2. Create hardlinks and symlinks; delete the original and observe behavior.
3. Fill a disk and observe `ENOSPC`.

@quiz: In Unix-like systems, what structure usually stores file metadata without the filename?
@option: dentry
@correct: inode
@option: TLB
