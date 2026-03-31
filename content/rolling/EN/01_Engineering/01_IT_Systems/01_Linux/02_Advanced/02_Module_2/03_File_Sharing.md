@title: File Sharing: Samba (SMB) and NFS
@icon: 🤝
@description: Full interoperability. Configure Samba for Windows clients and NFS for fast Linux-to-Linux workloads.
@order: 3

# Samba (SMB) and NFS: design, security, and permissions

File sharing is **LPIC-2** and daily work in mixed Windows/Linux sites. You need **SMB** for Windows clients and **NFS** for native UNIX loads, including **UID/GID** and **Kerberos** in enterprise setups.

@section: 1. Samba: SMB/CIFS on Linux

**Packages:** `samba`, `samba-common-bin` (Debian); `samba` on RHEL.

**`/etc/samba/smb.conf`:**

* **`[global]`:** `workgroup`, `security = user` (or `ADS` for a domain), `realm` with Kerberos.
* **Shares:** `path`, `valid users`, `read only`, `guest ok` (avoid in production).

**Samba passwords** are separate from UNIX passwords:

```bash
sudo smbpasswd -a user
```

**Permissions:** NTFS-like ACLs with `vfs objects = acl_xattr` and a filesystem that supports extended attributes.

**Diagnostics:**

```bash
testparm
sudo smbclient -L localhost -U user
```

**Ports:** 445/tcp, legacy 139/tcp — open in **firewalld**/UFW.

**SELinux:** booleans `samba_share_nfs`, `samba_share_t` on published paths.

**Linux CIFS client** typical mount:

```bash
sudo mount -t cifs //server/share /mnt -o username=...,uid=1000,gid=1000,file_mode=0640,dir_mode=0750
```

`uid`/`gid` force mapping to a local user when the server does not provide the UNIX extensions you expect. **`multiuser`** and **`cifsacl`** matter with Active Directory.

@section: 2. NFS: v3 vs v4

**NFSv4** is default on modern systems: single **2049/tcp**, better **Kerberos** integration (`sec=krb5p`).

**Server (`/etc/exports`):**

```text
/srv/data 192.168.1.0/24(rw,sync,no_subtree_check,sec=sys)
```

**Apply:** `exportfs -ra`, `showmount -e localhost`.

**Client:**

```bash
sudo mount -t nfs4 server:/srv/data /mnt
```

**fstab:** `nfsvers=4`, `_netdev`, `x-systemd.automount` when appropriate.

@section: 3. Anonymous root and UIDs

NFS maps **numeric UID/GID**. If the client maps `user` to UID 1000 but the server disagrees, you see wrong owners or “nobody”. Mitigations:

* **LDAP/NIS** for consistent IDs.
* **`all_squash` / `root_squash`** to map to a safe user.

**Kerberos with NFSv4:** in corporate domains, `sec=krb5p` encrypts and authenticates users; requires valid tickets (`kinit`) and matching client/server config. More work than `sec=sys`, but avoids UID spoofing on untrusted networks.

@section: 4. Performance and reliability

* **`sync` vs `async`:** `sync` safer; `async` faster with risk.
* **`rsize`/`wsize`** tuning on mount.
* Unstable networks: prefer **TCP**, tune timeouts.

@section: 5. Alternatives

* **iSCSI** for block (databases).
* **GlusterFS/Ceph** for distributed storage (beyond this intro).

@section: 6. Lab

1. Mount a Samba share from Windows and from Linux (`smbclient`).
2. Export NFSv4 with `nfsvers=4`; test permissions with two users.
3. Document what happens if you create a file as root on a client when the export uses `root_squash`.
4. Compare latency and ease of use between **SMB** and **NFS** for the same data from Linux (lab).

@quiz: Which command sets the SMB password for an existing Linux user?
@option: passwd
@correct: smbpasswd -a
@option: samba-useradd

@quiz: What typical problem appears when local UIDs do not match between NFS client and server?
@option: Blocked port
@correct: Wrong ownership or files appearing as another user
@option: Invalid TLS

@quiz: Which file defines NFS exports on the server?
@option: smb.conf
@correct: /etc/exports
@option: fstab
