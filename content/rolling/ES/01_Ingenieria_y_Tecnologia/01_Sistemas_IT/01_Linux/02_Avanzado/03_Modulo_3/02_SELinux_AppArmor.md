@title: SELinux & AppArmor: Control de Acceso Obligatorio
@icon: 👮
@description: Seguridad más allá de los permisos rwx. Cómo confinar procesos para que, si son hackeados, el daño sea mínimo.
@order: 2

# MAC: SELinux y AppArmor en profundidad

Los permisos UNIX (rwx) son **DAC** (Discretionary Access Control): el dueño decide. **MAC** (Mandatory Access Control) añade políticas del sistema que **ni siquiera root** puede ignorar si la política lo prohíbe (según modo). En **LPIC-2** y en **RHCE** SELinux es tema obligatorio.

@section: 1. AppArmor (Ubuntu/Debian/SUSE)

Modelo basado en **rutas**: perfiles en `/etc/apparmor.d/`.

**Modos:**

*   **enforce:** bloquea violaciones.
*   **complain:** solo registra (útil para crear perfiles).

```bash
sudo aa-status
sudo aa-enforce /usr/sbin/nginx
sudo aa-complain /usr/bin/mysqld
```

**Logs:** `dmesg`, `journalctl`, a veces `/var/log/audit/` si auditd integra mensajes.

**Generar perfil nuevo:** `aa-genprof comando` (modo aprendizaje en laboratorio).

**Perfiles y abstracciones:** en `/etc/apparmor.d/abstractions/` hay piezas reutilizables (red, TLS, etc.). Incluir abstracciones reduce ruido y mantiene políticas legibles. **`aa-logprof`** sugiere reglas a partir de denegaciones recientes.

@section: 2. SELinux (RHEL/Fedora/CentOS)

Basado en **etiquetas** (contextos): usuario, rol, tipo, nivel (MLS/MCS opcional).

**Modos (`/etc/selinux/config`):**

*   **enforcing** — política activa.
*   **permissive** — registra pero no bloquea (diagnóstico).
*   **disabled** — requiere reinicio para volver a habilitar.

```bash
getenforce
sudo setenforce 0   # temporal a permissive
```

**Contextos de proceso y archivo:**

```bash
ps auxZ
ls -Z /var/www/html
```

Si copias un archivo con `cp`, puede heredar contexto incorrecto; **`mv`** preserva o no según FS. **`restorecon -Rv ruta`** reaplica reglas por defecto.

**Booleans** (interruptores de política):

```bash
getsebool -a | grep httpd
sudo setsebool -P httpd_can_network_connect 1
```

**Puertos:** SELinux sabe qué puertos puede abrir cada tipo (`semanage port -l`).

**Contextos persistentes:** `chcon` cambia en caliente pero puede perderse al restaurar políticas; lo correcto en producción es **`semanage fcontext -a`** + **`restorecon`**. Ejemplo: añadir `/srv/sitios(/.*)?` con tipo `httpd_sys_content_t`.

**`dontaudit`:** reglas que ocultan denegaciones esperadas; si algo “falla en silencio”, prueba temporalmente `semodule -DB` para desactivar dontaudits (solo diagnóstico, luego revierte).

@section: 3. Herramientas de diagnóstico

*   **`audit2allow`:** genera módulos de política desde denegaciones (usar con cautela).
*   **`sealert` / setroubleshoot:** mensajes en lenguaje casi natural en el log.
*   **Regla práctica:** si algo “funciona con SELinux permissive” pero no en enforcing, **no** desactives SELinux permanentemente; ajusta contexto o boolean.

@section: 4. Tabla comparativa rápida

| Tema | AppArmor | SELinux |
| :--- | :--- | :--- |
| Modelo | Rutas, perfiles | Etiquetas, políticas |
| Curva | Suele ser más suave | Más potente, más complejo |
| Exámenes RH | Menos | Muy frecuente |

@section: 5. Laboratorio

1.  Crea un archivo en `/root` y cópialo a `/var/www/html`; intenta servirlo con httpd. Observa `ls -Z` y corrige con `restorecon`.
2.  Lista booleans relacionados con `httpd` y documenta qué activa cada uno.
3.  Usa `ausearch -m avc -ts recent` tras un fallo y relaciona el mensaje con `sealert` si está instalado.
4.  Explica con tus palabras por qué “poner SELinux en permissive para siempre” es mala práctica en servidores expuestos.

@quiz: ¿Qué comando reaplica contextos SELinux por defecto en un árbol de directorios?
@option: chcon -R
@correct: restorecon -Rv
@option: chmod -R

@quiz: En AppArmor, ¿qué modo solo registra violaciones sin bloquear?
@option: enforce
@correct: complain
@option: disabled

@quiz: ¿Qué muestra `ls -Z` frente a `ls -l`?
@option: Solo el tamaño
@correct: Contextos de seguridad MAC (SELinux)
@option: inode
