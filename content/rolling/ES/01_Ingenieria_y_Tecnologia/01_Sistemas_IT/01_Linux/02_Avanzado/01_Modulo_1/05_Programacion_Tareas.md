
@title: Tareas programadas: cron, at y timers de systemd
@icon: ⏰
@description: Automatizar con crontab y at; timers y transient units en systemd para sustituir o complementar cron.
@order: 5

# Automatización temporal: cron, at y systemd timers

Un servidor que requiere que un humano pulse botones para hacer mantenimiento no es un servidor, es una mascota.
La regla número uno del SysAdmin es: **Si tienes que hacerlo más de dos veces, automatízalo.**

Linux tiene herramientas ancestrales y modernas para ejecutar tareas en el tiempo.
*   "Ejecuta el backup a las 3 AM".
*   "Limpia los temporales cada viernes".
*   "Ejecuta este script 15 minutos después de arrancar".
*   "Apaga la máquina dentro de 2 horas".

En este módulo, dominaremos el clásico **Cron**, entenderemos por qué a veces falla misteriosamente (el maldito entorno), y aprenderemos a usar la herramienta superior moderna que gestiona el tiempo con precisión quirúrgica: **Systemd Timers**.

@section: 1. Cron: El Clásico Inmortal

El demonio `cron` (cuyo nombre viene de Chronos, el dios griego del tiempo) es una de las piezas de software más antiguas y fiables de Unix. Se despierta cada minuto, revisa sus listas de tareas y ejecuta lo que toque. Es simple, efectivo y está en todas partes.

### 1.1 Arquitectura de Cron
El servicio se llama normalmente `cron` o `crond`.
Lee archivos de configuración llamados **crontabs** (tablas de cron).

Existen tres lugares principales donde viven estas tareas, y es vital no confundirlos:

1.  **Cron de Usuario (`crontab -e`):**
    *   Cada usuario tiene su propia tabla privada.
    *   Se edita con el comando `crontab -e` (nunca editando archivos a mano).
    *   Las tareas se ejecutan con los permisos (UID/GID) de ese usuario.
    *   No puedes dañar el sistema desde aquí (a menos que seas root).
    *   Los archivos reales suelen vivir en `/var/spool/cron/crontabs/`.

2.  **Cron del Sistema (`/etc/crontab`):**
    *   Es un archivo global único.
    *   Solo root puede tocarlo.
    *   **Diferencia clave:** Tiene una columna extra para especificar *con qué usuario* se ejecuta cada comando.

3.  **Cron Modular (`/etc/cron.d/`):**
    *   Es un directorio donde puedes soltar archivos de texto.
    *   Funciona igual que `/etc/crontab` (necesita usuario).
    *   Es el método preferido para scripts de instalación y paquetes, porque no tienes que editar un archivo existente, solo añades uno nuevo.

### 1.2 La Sintaxis Maldita (y Bendita)
Una línea de cron tiene 5 campos de tiempo y luego el comando. Si es un cron de sistema, tiene 6 (el usuario va antes del comando).

Estructura de usuario:
`m h dom mon dow comando`

Estructura de sistema:
`m h dom mon dow usuario comando`

1.  **m**: Minuto (0-59).
2.  **h**: Hora (0-23).
3.  **dom**: Día del Mes (1-31).
4.  **mon**: Mes (1-12).
5.  **dow**: Día de la Semana (0-7, donde 0 y 7 son Domingo).

**Operadores Especiales:**
*   `*` (Asterisco): Significa "Todos". En el campo hora significa "cada hora".
*   `,` (Coma): Lista de valores. `1,15,30` significa en el minuto 1, el 15 y el 30.
*   `-` (Guion): Rango. `1-5` significa del día 1 al 5.
*   `/` (Barra): Paso (Step). `*/10` significa "cada 10 unidades".

**Ejemplos para descifrar:**

*   `30 04 * * * backup.sh`
    *   "A las 04:30, cualquier día del mes, cualquier mes, cualquier día de la semana".
    *   Resumen: **Diario a las 4:30 AM**.

*   `00 17 * * 5 email.sh`
    *   "A las 17:00, cualquier día, cualquier mes, si el día de la semana es 5 (Viernes)".
    *   Resumen: **Cada viernes a las 5 PM**.

*   `*/10 * * * * check.sh`
    *   "En cualquier minuto divisible por 10 (0, 10, 20, 30, 40, 50)".
    *   Resumen: **Cada 10 minutos**.

*   `00 00 1 * * facturas.sh`
    *   "A media noche (00:00) del día 1 de cualquier mes".
    *   Resumen: **Mensual, el día 1**.

*   `00 09-18 * * 1-5 trabajo.sh`
    *   "En el minuto 0, de las horas 9 a la 18, de lunes (1) a viernes (5)".
    *   Resumen: **Cada hora en horario laboral**.

**Atajos (Keywords):**
Para los vagos, cron entiende algunas palabras mágicas que empiezan por `@`:
*   `@reboot`: Se ejecuta una vez al arrancar la máquina. (¡Muy útil!).
*   `@yearly` o `@annually`: Una vez al año (1 Enero).
*   `@monthly`: Una vez al mes.
*   `@weekly`: Una vez a la semana.
*   `@daily` o `@midnight`: Una vez al día.
*   `@hourly`: Una vez a la hora.

@quiz: ¿Cuál es la diferencia fundamental entre editar tu cron con `crontab -e` y editar `/etc/crontab`?
@option: `crontab -e` es solo para usuarios root.
@correct: `/etc/crontab` tiene una columna extra para especificar el usuario que ejecutará el comando, mientras que `crontab -e` asume el usuario actual.
@option: `/etc/crontab` usa una sintaxis XML.
@option: `crontab -e` requiere reiniciar el servicio, `/etc/crontab` no.

@section: 2. El Infierno del Entorno (Environment)

El 99% de las veces que un novato dice "mi script funciona en la terminal pero no en cron", la culpa es del **Entorno**.

Cuando tú abres una terminal y te logueas:
1.  Se carga `.bashrc` o `.profile`.
2.  Se define la variable `$PATH` (dónde están los programas).
3.  Se definen alias, colores y variables de usuario.

Cuando Cron ejecuta una tarea:
1.  **NO** carga `.bashrc`.
2.  **NO** carga tu perfil.
3.  El `$PATH` es mínimo (a menudo solo `/bin` y `/usr/bin`).
4.  No hay terminal interactiva (no puedes usar `read` ni programas que pidan input).

**Síntomas del fallo de entorno:**
*   El script usa `python` y cron no lo encuentra porque está en `/usr/local/bin/python`.
*   El script intenta escribir en una carpeta relativa (`./logs`) y falla porque cron no está en esa carpeta.
*   El script usa una variable `$MI_API_KEY` que definiste en tu `.bashrc` y cron la ve vacía.

**Soluciones de Ingeniería:**

1.  **Rutas Absolutas SIEMPRE:**
    *   Mal: `python3 script.py`
    *   Bien: `/usr/bin/python3 /home/juan/scripts/script.py`

2.  **Definir PATH en el crontab:**
    Puedes poner variables al principio del archivo crontab:
    ```bash
    PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
    SHELL=/bin/bash
    API_KEY=123456

    30 04 * * * /home/juan/backup.sh
    ```

3.  **Cargar el perfil manualmente (Truco Sucio pero efectivo):**
    Si tu script depende de mil cosas de tu perfil, fúrzalo a cargarse:
    `30 04 * * * source /home/juan/.bashrc && /home/juan/backup.sh`

4.  **Redirección de Salida (Logging):**
    Cron intenta enviar un email local si el comando produce salida o error. Hoy en día nadie lee el mail local. Si el script falla y no rediriges la salida, **nunca sabrás el error**.
    *   Obligatorio redirigir STDOUT y STDERR:
    `* * * * * /ruta/script.sh >> /var/log/mi_app.log 2>&1`

@quiz: Tu script de backup funciona manualmente, pero en cron falla silenciosamente. ¿Cuál es la causa más probable?
@option: Cron no tiene permisos de root.
@correct: Cron tiene un entorno de variables ($PATH) muy limitado y no encuentra los comandos o usa rutas relativas incorrectas.
@option: El script no tiene permisos de ejecución.
@option: Cron no soporta scripts de shell, solo binarios.

@section: 3. Anacron: Para los que duermen

Cron asume que el servidor está encendido 24/7.
Si tienes una tarea a las 3 AM y apagas tu portátil a las 11 PM para dormir... **la tarea se pierde**. Cron no tiene memoria. A las 3 AM el PC estaba apagado, y a las 9 AM cuando lo enciendes, las 3 AM ya pasaron. Mala suerte.

**Anacron** (Anachronistic Cron) soluciona esto.
No está diseñado para ejecutar cosas en un minuto exacto, sino con una frecuencia (diaria, semanal).

**Cómo funciona:**
1.  Cuando el PC arranca, Anacron se despierta.
2.  Mira un archivo de timestamps (`/var/spool/anacron/`).
3.  Pregunta: "¿Se ejecutó la tarea 'diaria' hoy?".
4.  Si la respuesta es NO (porque el PC estaba apagado), la ejecuta **ahora**.

**Retraso Aleatorio:**
Para evitar que tu portátil se vuelva lento nada más encenderlo porque intenta hacer 50 tareas atrasadas a la vez, Anacron introduce un `RANDOM_DELAY`. Espera unos minutos antes de empezar.

Las carpetas `/etc/cron.daily`, `/etc/cron.weekly` y `/etc/cron.monthly` suelen estar gestionadas por Anacron en sistemas de escritorio como Ubuntu o Fedora. Si pones un script en `cron.daily`, se ejecutará una vez al día, independientemente de si apagas el PC por la noche.

@section: 4. Systemd Timers: La Evolución Superior

Cron es genial, pero es tecnología de los 70. Tiene límites.
*   Precisión de solo 1 minuto (no puedes ejecutar algo cada 10 segundos).
*   Gestión de logs dispersa.
*   No gestiona dependencias ("Ejecuta esto solo si la red está online").

**Systemd Timers** reemplazan a cron en el mundo moderno.
El concepto es diferente: No hay una tabla. Hay **Unidades**.
Necesitas dos archivos para cada tarea programada:
1.  **El Servicio (`.service`):** Lo QUE se ejecuta.
2.  **El Timer (`.timer`):** CUÁNDO se ejecuta.

### Paso 1: Crear el Servicio
Supongamos que queremos ejecutar `backup.sh`.
Creamos `/etc/systemd/system/mi-backup.service`:

```ini
[Unit]
Description=Servicio de Backup para Timer

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```
*(Nota: No tiene sección `[Install]` porque no lo vamos a habilitar para que arranque al inicio, lo va a despertar el timer).*

### Paso 2: Crear el Timer
Creamos `/etc/systemd/system/mi-backup.timer`:

```ini
[Unit]
Description=Ejecuta el backup cada día a las 4 AM

[Timer]
# Equivalente a cron: "A las 4 am cada día"
OnCalendar=*-*-* 04:00:00

# PRECISIÓN y ENERGÍA
# Permite que el sistema atrase la tarea hasta 10 min para agrupar despertares de CPU
AccuracySec=10m

# PERSISTENCIA (El "Anacron" de Systemd)
# Si el PC estaba apagado, ejecútalo inmediatamente al arrancar
Persistent=true

# RETRASO ALEATORIO (Anti "Thundering Herd")
# Si tienes 1000 servidores con este timer, que no ataquen al servidor de backup a la vez
RandomizedDelaySec=5m

[Install]
WantedBy=timers.target
```

### Paso 3: Activar
```bash
$ sudo systemctl enable --now mi-backup.timer
```
*(Activamos el timer, no el servicio).*

### Tipos de Eventos en Timers
Systemd ofrece dos modos de medir el tiempo:

1.  **Monotónicos (Relativos):** Se basan en eventos, no en la hora del reloj. Son inmunes a cambios de hora o zonas horarias.
    *   `OnBootSec=15min`: Ejecutar 15 minutos después de arrancar.
    *   `OnUnitActiveSec=1h`: Ejecutar 1 hora después de la última vez que el servicio se ejecutó. (Ideal para bucles infinitos limpios).

2.  **Calendario (Realtime):** Se basan en fecha/hora (Wall Clock).
    *   `OnCalendar=Mon..Fri 10:00`: Lunes a Viernes a las 10.
    *   `OnCalendar=*-*-01 00:00:00`: El día 1 de cada mes.
    *   Sintaxis: `DíaSemana Año-Mes-Día Hora:Min:Seg`.

### Ventajas de Systemd Timers sobre Cron
1.  **Logs centralizados:** `journalctl -u mi-backup.service` te da la salida estándar y de error, con fechas exactas y metadatos.
2.  **Precisión:** Puedes programar tareas con precisión de microsegundos si quieres.
3.  **Dependencias:** En el `.service` puedes poner `After=network-online.target` para asegurarte de que tienes internet antes de intentar el backup. Si no hay red, espera. Cron simplemente fallaría.
4.  **Control de Recursos (Cgroups):** Puedes limitar la CPU (`CPUQuota`) o RAM (`MemoryMax`) del proceso de backup en el archivo `.service`. Cron no puede hacer esto fácilmente.

**Listar Timers activos:**
```bash
$ systemctl list-timers
```
Te muestra una tabla preciosa:
*   **NEXT:** Cuándo toca la próxima vez.
*   **LEFT:** Cuánto falta.
*   **LAST:** Cuándo se ejecutó la última vez.
*   **PASSED:** Cuánto tiempo ha pasado.
*   **UNIT:** El servicio que activa.

@quiz: ¿Qué opción en un archivo `.timer` de Systemd asegura que la tarea se ejecute si el ordenador estaba apagado a la hora programada (comportamiento tipo Anacron)?
@option: OnBootSec=true
@option: CatchUp=true
@correct: Persistent=true
@option: AlwaysRun=true

@section: 5. `at`: El Mayordomo de una sola vez

A veces no quieres repetir algo para siempre. Solo quieres decir: "Apaga el servidor dentro de 2 horas" o "Ejecuta este script a las 5 PM cuando ya me haya ido".
Cron no sirve para esto (tendrías que crear la tarea y luego acordarte de borrarla).
Para eso está **`at`**.

Instalación (no suele venir por defecto): `sudo apt install at`.

### Uso Interactivo
```bash
$ at 5pm
warning: commands will be executed using /bin/sh
at> /home/juan/apagar_luces.sh
at> echo "Luces apagadas" >> /tmp/log
at> (Pulsa Ctrl+D para guardar y salir)
```
Listo. El sistema te responde: `job 1 at Mon Oct 20 17:00:00 2025`.

### Uso con Tuberías (Scripting)
Puedes mandar tareas a `at` sin entrar en modo interactivo:
```bash
$ echo "sudo apt update && sudo reboot" | at 03:00 tomorrow
```
Ideal para programar reinicios nocturnos rápidos.

### Sintaxis de Tiempo Flexible
`at` es muy listo entendiendo el tiempo humano:
*   `at now + 10 minutes`
*   `at 4pm + 3 days`
*   `at 10:00 July 31`
*   `at teatime` (Sí, existe, son las 16:00).

### Gestión de la Cola
*   **`atq`**: Ver la cola de trabajos pendientes.
*   **`atrm [ID]`**: Borrar una tarea (ej: `atrm 5`).
*   **`batch`**: Es un comando especial relacionado con `at`. Ejecuta el comando **solo cuando la carga del sistema (Load Average) baje de 0.8**. Ideal para tareas de fondo pesadas (como indexar bases de datos) que no quieres que ralenticen el servidor si está ocupado.

@section: 6. `systemd-run`: Tareas Transitorias Modernas

Si te gusta `at` pero quieres la potencia de Systemd (logs, cgroups), usa `systemd-run`.
Permite crear unidades "efímeras" (transient units) sin crear archivos de texto.

**Ejecutar algo ahora mismo (pero encapsulado):**
```bash
$ sudo systemd-run --unit=mi-compilacion --property=MemoryMax=1G ./compilar_todo.sh
```
Te devolverá: `Running as unit: mi-compilacion.service`.
*   El proceso corre en segundo plano.
*   Si se cierra la terminal, sigue corriendo.
*   Si se pasa de 1GB de RAM, systemd lo mata (gracias a la propiedad).
*   Puedes ver sus logs: `journalctl -u mi-compilacion -f`.
*   Puedes pararlo: `systemctl stop mi-compilacion`.

**Programar para el futuro (Timer efímero):**
```bash
$ sudo systemd-run --on-active=1h /bin/touch /tmp/archivo
```
*(Ejecuta esto dentro de 1 hora).*

@section: 7. Laboratorio: El Backup Perfecto

Vamos a crear un sistema de backup robusto usando Systemd, aplicando todo lo aprendido.

**Escenario:**
Queremos comprimir la configuración del sistema (`/etc`) y guardarla en `/backups`.
*   Debe ocurrir cada día a las 02:00 AM.
*   Si el servidor estaba apagado, debe hacerse al arrancar.
*   Debe borrar los backups de más de 7 días de antigüedad.
*   Debe tener baja prioridad de CPU para no molestar.

**1. El Script (`/usr/local/bin/backup_etc.sh`)**
```bash
#!/bin/bash
DATE=$(date +%F)
DEST="/backups"

# Crear destino si no existe
mkdir -p $DEST

# Comprimir con fecha
tar -czf $DEST/etc-$DATE.tar.gz /etc 2>/dev/null

# Borrar viejos (más de 7 días)
find $DEST -name "etc-*.tar.gz" -mtime +7 -delete

echo "Backup completado para $DATE"
```
*(No olvides darle permisos de ejecución: `chmod +x`)*.

**2. El Servicio (`/etc/systemd/system/etc-backup.service`)**
```ini
[Unit]
Description=Backup de /etc

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup_etc.sh
# Hacemos que sea "amable" con la CPU
Nice=19
IOSchedulingClass=idle
```

**3. El Timer (`/etc/systemd/system/etc-backup.timer`)**
```ini
[Unit]
Description=Trigger diario para backup de /etc

[Timer]
# A las 2 AM
OnCalendar=*-*-* 02:00:00
# Persistencia por si estaba apagado
Persistent=true
# Pequeño retraso aleatorio de hasta 5 min
RandomizedDelaySec=5m

[Install]
WantedBy=timers.target
```

**4. Activación**
```bash
$ sudo systemctl enable --now etc-backup.timer
```
*(Nota: No habilitamos el .service, solo el .timer. El timer es quien despertará al servicio).*

**5. Verificación**
Comprueba que tu timer está en la lista y preparado:
```bash
$ systemctl list-timers
```
¡Listo! Has creado un sistema de backup profesional, resiliente y gestionado.

@section: Resumen / Cheat Sheet

| Herramienta | Uso Ideal | Comando Clave |
| :--- | :--- | :--- |
| **Cron** | Tareas simples, recurrentes, legacy. | `crontab -e` |
| **Anacron** | Tareas diarias en PCs que se apagan. | `/etc/anacrontab` |
| **Systemd Timers** | Tareas complejas, dependencias, logs, precisión. | `systemctl list-timers` |
| **at** | Tarea única en el futuro ("Haz esto a las 5"). | `at 5pm` |
| **systemd-run** | Tareas ad-hoc con control de recursos. | `systemd-run ...` |

**Consejo de Experto:**
Para cosas rápidas y personales, `crontab -e` sigue siendo el rey por su simplicidad.
Para infraestructura, servicios, backups críticos y servidores de producción, migra a **Systemd Timers**. La visibilidad que te dan los logs y el control de errores vale la pena la complejidad extra.