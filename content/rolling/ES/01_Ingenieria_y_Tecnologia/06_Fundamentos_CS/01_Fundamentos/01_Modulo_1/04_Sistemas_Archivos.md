@title: Sistemas de archivos: inodos, journaling y consistencia
@icon: 📁
@description: ext4, copy-on-write (ZFS/Btrfs), permisos y enlaces.
@order: 4

# Sistemas de archivos: metadatos, consistencia y rendimiento

Un **sistema de archivos** organiza bloques en disco como archivos y directorios. **inodes** almacenan metadatos; directorios mapean nombres → inode. **Journaling** (ext4) o **copy-on-write** (Btrfs, ZFS) recuperan ante cortes de energía. Esta lección cubre **permisos** Unix, **ACLs**, **symlinks** vs **hardlinks**, y **VFS** en el kernel.

@section: Estructuras

**Superblock**, **bitmap** de bloques libres, **tabla de inodes**. **Extents** agrupan bloques contiguos para archivos grandes.

@section: Journaling

Registro de transacciones antes de aplicar cambios al FS principal; modos **ordered**, **journal**, **writeback** con distinto equilibrio seguridad/rendimiento.

@section: COW

**Btrfs/ZFS** nunca sobrescriben in-place: nuevas versiones, snapshots baratos.

@section: Permisos

`rwx` para user/group/other; **umask** define máscara de creación. **setuid** implica riesgo de seguridad.

@section: Laboratorio sugerido

1. Inspecciona `stat`, `ls -i`, `df -T` en Linux.
2. Crea hardlink y symlink; borra original y observa comportamiento.
3. Simula llenado de disco y observa `ENOSPC`.

@quiz: ¿Qué estructura suele almacenar metadatos de archivo en Unix-like sin el nombre?
@option: dentry
@correct: inode
@option: TLB
