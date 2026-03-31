@title: Recuperación ante Desastres (Rescue Mode)
@icon: 🚑
@description: Has roto el sistema. No arranca. No hay SSH. Aprende a usar chroot, editar GRUB y resetear contraseñas de root perdidas.
@order: 3

# Recuperación ante desastres: boot, chroot y backups

Romper `/etc/fstab`, olvidar la clave de root o borrar `/boot` son escenarios reales. **LPIC-2** espera que domines **modo rescate**, **chroot** y conceptos de **imagen de sistema**.

@section: 1. GRUB: `init=/bin/bash` y systemd

**Legacy:** añadir `init=/bin/bash` al kernel en GRUB → shell de root; **remount rw** `mount -o remount,rw /` → `passwd`.

**systemd:** alternativas modernas:

*   **`rd.break`** en la línea del kernel (interrumpe antes de switch_root, típico RHEL).
*   **`systemd.debug-shell`** para depuración (¡solo laboratorio!).

**Cuidado:** SELinux puede requerir **`touch /.autorelabel`** tras cambios fuera de política.

@section: 2. Live USB y chroot completo

1.  Arranca ISO de rescate (misma familia de distro que el instalado).
2.  Identifica particiones: `lsblk`, `blkid`.
3.  Monta root en `/mnt`; si hay **boot/efi** separado, monta en `/mnt/boot/efi`.
4.  Bind mounts:

```bash
for d in dev proc sys run; do mount --bind /$d /mnt/$d; done
```

5.  `chroot /mnt` (o `arch-chroot` en Arch).

Dentro: `grub2-install`, `update-grub`, `dracut`/`mkinitcpio`, **reinstalar kernel**.

**Btrfs/ZFS:** snapshots de rollback antes de actualizaciones (si los configuraste).

@section: 3. Backups: 3-2-1 y pruebas de restore

*   **3** copias de datos, **2** medios distintos, **1** fuera de sitio.
*   **Imagen completa:** `dd`, `Clonezilla`, snapshots de hipervisor.
*   **Archivos:** `rsync`, `borgbackup`, `restic`.
*   **Bases de datos:** dumps lógicos (`mysqldump`, `pg_dump`) + copias de WAL/binlog según RPO.

**Regla de oro:** un backup sin **restore** probado **no existe**.

@section: 4. Initramfs roto

Si el kernel no encuentra módulos o raíz:

```bash
dracut -f   # RHEL
update-initramfs -u -k all   # Debian
```

Desde chroot tras montar el sistema dañado.

@section: 5. LUKS / cifrado de disco

Si `/` está en **LUKS**, necesitas **initramfs** con hooks y passphrase en arranque. Rescate requiere desbloquear el volumen: `cryptsetup luksOpen`.

@section: 6. Plan de documentación

Para cada servidor crítico: **procedimiento de rescate** en un archivo fuera del DC (runbook), con **UUID** de particiones y versión de bootloader.

@section: 7. Objetivos de systemd en rescate

Además de `init=/bin/bash` (peligroso y poco fino), systemd ofrece destinos explícitos:

*   **`systemd.unit=rescue.target`:** shell de root temprana, servicios mínimos.
*   **`systemd.unit=emergency.target`:** aún más minimalista; solo consola de mantenimiento.

Desde ahí puedes **remontar** `/` lectura-escritura, arreglar `fstab`, regenerar initramfs o desbloquear LUKS. Documenta en tu runbook **qué tecla** abre el menú de GRUB en tu hardware (Esc, Shift, etc.).

@section: 8. Cuando el initramfs no encuentra la raíz

Síntomas: mensaje **“unable to find root”**, caída al **initramfs shell**. Causas frecuentes: UUID incorrecto en la línea del kernel, módulo de disco faltante, o arranque desde snapshot Btrfs equivocado. Desde el shell de initramfs:

*   Revisa `blkid` si está disponible o inspecciona `/dev`.
*   Monta manualmente la raíz y corrige `fstab` / `grub.cfg` desde **chroot** en live media si hace falta.

@section: 9. Btrfs y snapshots de rescate

Si instalaste con **Btrfs** y configuraste snapshots (p. ej. con **snapper**), antes de reinstalar prueba **rollback** al snapshot pre-actualización desde el menú de arranque o con `btrfs subvolume list` y `btrfs subvolume set-default`. Es la diferencia entre **cinco minutos** y **cinco horas** de trabajo.

@section: 10. Laboratorio ampliado

1.  Rompe a propósito `/etc/fstab` en VM (comenta la línea de `/` o pon UUID falso) y recupéralo con live ISO + chroot; cronometra el RTO.
2.  Documenta el tiempo de recuperación (RTO) medido y qué habrías hecho distinto con un **backup de `/etc`** en el mismo día.
3.  (Opcional) Arranca con `rescue.target` y practica `mount -o remount,rw /` sobre la raíz real.
4.  Escribe una **checklist** de verificación post-restauración: red, DNS, servicios críticos, espacio en disco.

@quiz: Tras montar el sistema dañado en `/mnt`, ¿qué comando cambia la raíz efectiva a ese árbol?
@option: pivot_root
@correct: chroot /mnt (o systemd-nspawn en flujos modernos)
@option: mount --move

@quiz: ¿Qué práctica valida que los backups son útiles?
@option: Tener 10 copias en el mismo disco
@correct: Restaurar periódicamente en entorno de prueba (restore drill)
@option: Comprimir siempre con gzip

@quiz: ¿Qué archivo puede requerir `touch /.autorelabel` tras editar contextos desde rescate en sistema SELinux?
@option: /etc/hosts
@correct: (arranque) autorelabel en raíz para reetiquetar
@option: /boot/grub2/grub.cfg
