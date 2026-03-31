@title: Auditoría del Sistema: auditd y logs forenses
@icon: 🕵️
@description: ¿Quién borró ese archivo? ¿Quién cambió la hora? Configura auditd para rastrear cada llamada al sistema y tener una traza forense inmutable.
@order: 4

# Linux Audit (`auditd`): trazabilidad y cumplimiento

Los logs de aplicación (`/var/log/auth.log`) cuentan **eventos de alto nivel**. **auditd** intercepta **llamadas al kernel** (syscalls) y permite responder en auditorías: “¿quién ejecutó `chmod` sobre este binario?” o “¿quién leyó `/etc/shadow`?”.

@section: 1. Arquitectura

*   **auditd** lee reglas de `/etc/audit/audit.rules` (o fragmentos en `/etc/audit/rules.d/`).
*   Los eventos van a `/var/log/audit/audit.log` (formato detallado).
*   Herramientas: **`auditctl`** (carga dinámica), **`ausearch`** (consulta), **`aureport`** (agregados).

**RHEL:** servicio `auditd`; **Debian/Ubuntu:** idéntico; en contenedores minimalistas a veces falta — verifica.

@section: 2. Reglas: archivos, syscalls y ejecutables

**Vigilar archivo (watch):**

```bash
sudo auditctl -w /etc/passwd -p wa -k passwd_cambios
```

*   `-p wa`: write + attribute change.
*   `-k`: clave para búsquedas.

**Vigilar syscall (ej. `execve`):** reglas avanzadas en `audit.rules` para detectar ejecución de binarios sensibles.

**Persistencia:** reglas en `/etc/audit/rules.d/*.rules` y `augenrules --load` o reinicio de `auditd`.

@section: 3. Consultas forenses

```bash
sudo ausearch -k passwd_cambios
sudo ausearch -ui 1000 -ts today
sudo aureport --auth --summary
```

**Campos útiles:** `auid` (login UID), `uid` (efectivo), `exe`, `success=yes|no`.

@section: 4. Integración con systemd / journal

`journalctl` puede mostrar mensajes relacionados; para **cadena de custodia** en incidentes, muchas organizaciones **reenvían** audit a SIEM (rsyslog, Fluent Bit). `auditd` es la fuente **autoritativa** de syscalls.

@section: 5. Rendimiento y riesgos

Demasiadas reglas = **sobrecarga** de I/O y CPU. Reglas mal diseñadas generan **ruido** inútil. Prueba en entorno de staging; usa **keys** consistentes.

@section: 6. Comparativa rápida: auditd vs syslog

| Aspecto | auditd | syslog tradicional |
| :--- | :--- | :--- |
| Granularidad | syscalls, inodos | mensajes de aplicación |
| Integridad | altamente detallado | depende del daemon |
| Uso típico | PCI-DSS, investigación | operación diaria |

@section: 7. Reglas avanzadas: syscalls y listas

Además de `-w` sobre archivos, puedes auditar **llamadas al sistema** concretas. Patrón típico en `audit.rules`:

```text
-a always,exit -F arch=b64 -S chmod -S fchmod -S fchmodat -F auid>=1000 -F auid!=4294967295 -k permisos_criticos
```

*   `-a always,exit`: registra al **salir** de la syscall (sabes si tuvo éxito).
*   `-F arch=b64`: arquitectura (en sistemas de 64 bits; a veces también `b32`).
*   `-S`: lista de syscalls (`unlink`, `rename`, `execve`… consulta `ausyscall --dump` para el mapa de tu kernel).
*   `-F auid>=1000`: filtra actividad de usuarios “humanos” (no solo daemon con auid `-1` o `4294967295` = *unset*).

**Listas de exclusión:** en entornos ruidosos se combinan reglas de inclusión con `exclude` o se sube el filtro por **key** para correlación en SIEM.

@section: 8. `auditd.conf`: espacio, retención y rendimiento

`/etc/audit/auditd.conf` controla rotación (`max_log_file`, `max_log_file_action = ROTATE`), retención y **backlog**. Si el kernel pierde eventos verás `backlog` en logs o mensajes de “rate limit”. En sistemas muy cargados:

*   Aumentar el **buffer** del kernel (`-b` en reglas, con cuidado).
*   Reducir reglas redundantes.
*   Enviar copia a syslog con `dispatcher` solo si tu SIEM lo consume así (duplica I/O).

@section: 9. Cadena de custodia y cumplimiento normativo

Para **PCI-DSS**, **ISO 27001** o auditorías internas, lo que importa es: **quién** hizo **qué**, **cuándo**, y que el log **no** sea editable por el mismo administrador que se investiga. `audit.log` en modo append-only (atributo `chattr +a` en el fichero, o envío inmediato a un **log host** remoto con firma) es un patrón habitual. Compara con `journald` **forwarding** a servidor central: útil, pero no sustituye la granularidad syscall de `auditd` cuando el requisito lo exige.

@section: 10. Laboratorio ampliado

1.  Añade una regla temporal sobre `/etc/hosts` y provoca un cambio; busca con `ausearch -k` y anota `auid`, `uid`, `comm`.
2.  Genera `aureport --failed` tras varios `su` fallidos y cruza con `ausearch -m USER_AUTH`.
3.  Lista syscalls disponibles con `ausyscall x64 chmod` y diseña (solo en papel o VM) una regla que audite borrados en un directorio de proyectos.
4.  Comprueba el tamaño de `/var/log/audit/` y la política de rotación en `auditd.conf`.

@quiz: ¿Qué herramienta consulta el log binario de auditd filtrando por clave `-k`?
@option: grep /var/log/syslog
@correct: ausearch
@option: journalctl --audit

@quiz: ¿Qué opción de `auditctl -w` define lectura, escritura o cambio de atributos?
@option: -k
@correct: -p (permisos rwxa)
@option: -f

@quiz: ¿Por qué no vigilar “todo el sistema” con reglas syscall globales en producción sin filtro?
@option: Ahorra espacio
@correct: Genera volumen masivo de eventos y degrada rendimiento
@option: No es posible técnicamente
