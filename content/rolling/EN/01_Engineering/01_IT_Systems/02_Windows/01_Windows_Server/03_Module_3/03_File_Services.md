@title: File Services: NTFS Permissions and SMB Shares
@icon: 📁
@description: Building secure and efficient shared folders.
@order: 3

# NTFS permissions and SMB shares

Effective access is the **most restrictive** combination of **share** and **NTFS** permissions. Assign via **groups**, not individual users, except exceptions.

@section: Share vs NTFS

*   **Share permissions** gate network access to the share.
*   **NTFS ACLs** gate access to files on disk.

**Pattern:** permissive share (Authenticated Users) + detailed NTFS on folders.

@section: ABE

**Access Based Enumeration** hides folders users cannot list—reduces data leakage of project names.

@section: FSRM

**File Server Resource Manager** quotas and file screens for compliance shares.

@section: Shadow copies

**Previous Versions** helps recover user files quickly—**not** a replacement for backups.

@quiz: How is effective permission calculated for SMB + NTFS?
@option: Sum of both
@correct: Most restrictive of share and NTFS
@option: Share wins always

@quiz: What feature hides folders users cannot access in a share?
@option: BitLocker
@correct: Access Based Enumeration
@option: DFS alone
