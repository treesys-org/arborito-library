@title: Compartir Archivos: Samba (SMB) y NFS
@icon: 🤝
@description: Interoperabilidad total. Configura Samba para servir archivos a clientes Windows y NFS para velocidad pura entre servidores Linux.
@order: 3

# Samba (SMB) y NFS: diseño, seguridad y permisos

Compartir archivos en Linux es **LPIC-2** y día a día en entornos mixtos Windows/Linux. Debes dominar **SMB** para clientes Windows y **NFS** para cargas UNIX nativas, incluidos **UID/GID** y **seguridad Kerberos** en entornos corporativos.

@section: 1. Samba: SMB/CIFS en Linux

**Paquetes:** `samba`, `samba-common-bin` (Debian); `samba` en RHEL.

**`/etc/samba/smb.conf`:**

*   **`[global]`:** `workgroup`, `security = user` (o `ADS` para dominio), `realm` si usas Kerberos.
*   **Shares:** `path`, `valid users`, `read only`, `guest ok` (evitar en producción).

**Usuarios Samba** son independientes de la contraseña UNIX:

```bash
sudo smbpasswd -a usuario
```

**Permisos:** ACL NTFS-like con `vfs objects = acl_xattr` y **filesystem** con soporte extended attributes.

**Diagnóstico:**

```bash
testparm
sudo smbclient -L localhost -U usuario
```

**Puertos:** 445/tcp, 139/tcp legacy; **firewalld**/`ufw` deben permitirlos.

**SELinux:** booleans `samba_share_nfs`, contextos `samba_share_t` en rutas publicadas.

**Cliente Linux con CIFS:** montaje típico:

```bash
sudo mount -t cifs //servidor/recurso /mnt -o username=...,uid=1000,gid=1000,file_mode=0640,dir_mode=0750
```

`uid`/`gid` fuerzan el mapeo a un usuario local cuando el servidor no provee UNIX extensions como esperas. **`multiuser`** y **`cifsacl`** entran en juego con Active Directory.

@section: 2. NFS: v3 vs v4

**NFSv4** por defecto en sistemas modernos: un solo puerto **2049/tcp**, mejor integración con **Kerberos** (`sec=krb5p`).

**Servidor (`/etc/exports`):**

```text
/srv/datos 192.168.1.0/24(rw,sync,no_subtree_check,sec=sys)
```

**Aplicar:** `exportfs -ra`, `showmount -e localhost`.

**Cliente:**

```bash
sudo mount -t nfs4 servidor:/srv/datos /mnt
```

**fstab:** opciones `nfsvers=4`, `_netdev`, `x-systemd.automount` si aplica.

@section: 3. Raíz anónima y UID

NFS **honra UID/GID numéricos**. Si el cliente mapea `usuario` a UID 1000 pero el servidor tiene otro, verás “nobody” o permisos incorrectos. Soluciones:

*   **LDAP/NIS** para UID/GID coherentes.
*   **`all_squash`/`root_squash`** para mapear a un usuario seguro.

**Kerberos con NFSv4:** en dominios corporativos, `sec=krb5p` cifra el tráfico y autentica usuarios; requiere tickets válidos (`kinit`) y configuración coherente en cliente y servidor. Es más trabajo que `sec=sys`, pero evita spoofing de UID en redes no confiables.

@section: 4. Rendimiento y fiabilidad

*   **`sync` vs `async`:** `sync` más seguro; `async` más rápido con riesgo.
*   **Rsize/wsize** en montaje para tuning.
*   **NFS sobre red inestable:** considera **TCP**, timeouts.

@section: 5. Alternativas

*   **iSCSI** para bloque (bases de datos).
*   **GlusterFS/Ceph** para almacenamiento distribuido (fuera de alcance introductorio).

@section: 6. Laboratorio

1.  Monta un share Samba desde Windows y desde Linux (`smbclient`).
2.  Exporta NFSv4 y monta con `nfsvers=4`; prueba permisos cruzados con dos usuarios.
3.  Documenta qué ocurre si en el cliente creas un archivo como root sobre un export con `root_squash`.
4.  Compara latencia y facilidad entre montar el mismo dato vía **SMB** y vía **NFS** desde Linux (laboratorio).

@quiz: ¿Qué comando añade la contraseña SMB de un usuario ya existente en Linux?
@option: passwd
@correct: smbpasswd -a
@option: samba-useradd

@quiz: ¿Qué problema típico aparece cuando UID locales no coinciden entre cliente y servidor NFS?
@option: Puerto bloqueado
@correct: Permisos incorrectos o archivos mostrados como de otro usuario
@option: TLS inválido

@quiz: ¿Qué archivo define los export NFS en el servidor?
@option: smb.conf
@correct: /etc/exports
@option: fstab
