@title: systemd: unidades, objetivos y servicios en producción
@icon: 🚀
@description: PID 1 con systemd: unit files, dependencias, systemctl, journal y nociones de cgroups para operación LPIC-2.
@order: 1

# systemd: unidades, arranque y gestión de servicios

Trabajarás **systemd** como PID 1: **unit files** (`.service`, `.target`, …), dependencias y orden de arranque, `systemctl`/`journalctl`, y cómo encajan **cgroups** y la supervisión de servicios en entornos LPIC-2 y producción.

@section: Mapa LPIC-2 — Módulo 1 (referencia de cobertura)

Este módulo se alinea con competencias típicas **LPIC-2 (200 series)** en **administración del sistema** y **dominio de systemd**:

*   **200.1 medidas de arranque y recuperación:** objetivos, `rescue`, `emergency`, depuración de unidades.
*   **200.2 diseño de particiones y LVM:** lecciones de discos y RAID.
*   **200.3 kernel y módulos:** compilación y parámetros.
*   **200.4 automatización de tareas:** timers y `cron`/`at` (programación de tareas).
*   **Práctica RHEL:** `systemctl` + `journalctl` es el estándar en examen; **Debian/Ubuntu** idéntico en systemd moderno.

Bienvenido a las ligas mayores de la administración de sistemas Linux.

En el nivel Junior, aprendiste que escribir `sudo systemctl start apache2` enciende el servidor web. Eso es como saber girar la llave de un coche. Sabes conducirlo, pero no tienes ni idea de lo que ocurre bajo el capó. Si el coche no arranca al girar la llave, estás perdido.

En este nivel Avanzado, vamos a abrir el capó, desmontar el motor pieza a pieza y aprender a trucarlo.

**Systemd** es, sin lugar a dudas, el componente más importante, controvertido y poderoso del ecosistema Linux moderno. Ya no es solo un "sistema de inicio" (init system) que arranca procesos al encender el PC. Se ha convertido en una suite de administración de sistemas masiva que ha absorbido responsabilidades críticas que antes estaban dispersas:
*   Gestión de logs (**journald**).
*   Gestión de dispositivos (**udev**).
*   Gestión de redes (**networkd**).
*   Resolución de nombres DNS (**resolved**).
*   Cronogramas y temporizadores (**timers**).
*   Contenedores ligeros (**nspawn**).
*   Gestión de sesiones de usuario (**logind**).

Para un SysAdmin Senior, entender Systemd no es opcional. Es el tejido conectivo que mantiene unido el sistema operativo. Es el primer proceso que nace (**PID 1**) y el último que muere. Si Systemd falla, el Kernel entra en pánico y el sistema muere.

En esta guía monumental, vamos a diseccionar la anatomía de una Unidad, entenderemos el complejo grafo de dependencias que permite arrancar el sistema en paralelo en segundos, analizaremos el tiempo de arranque al milisegundo, aprenderemos a limitar recursos con Cgroups para que un proceso no congele tu servidor, y escribiremos nuestros propios servicios blindados para producción.

Prepárate. Vamos a dominar al Dios de la máquina.

@section: 1. Historia y Filosofía: ¿Por qué Systemd?

Para entender Systemd, hay que entender qué había antes y por qué era un desastre.

### El Viejo Rey: SysVinit
Durante décadas, Linux usó **SysVinit**. Era simple. Era una colección de scripts de Bash en `/etc/init.d/`.
Para arrancar el sistema, SysVinit ejecutaba estos scripts **uno por uno**, en orden secuencial.
1.  Arrancar Red. (Esperar a que termine).
2.  Arrancar Disco. (Esperar).
3.  Arrancar Base de Datos. (Esperar).
4.  Arrancar Web.

**El Problema:**
*   **Lentitud:** Si la red tardaba 10 segundos en obtener IP, todo el arranque se detenía 10 segundos. Las CPUs modernas tienen muchos núcleos, pero SysVinit solo usaba uno. Era un desperdicio.
*   **Fragilidad:** Los scripts de Bash son propensos a errores, difíciles de mantener y varían entre distribuciones.
*   **Falta de Control:** Si un servicio moría, SysVinit no siempre se enteraba ni podía reiniciarlo.

### La Revolución: Systemd
Lennart Poettering diseñó Systemd con una idea radical: **Paralelismo Agresivo**.
Systemd no arranca cosas en fila. Arranca todo a la vez.

¿Cómo es posible? ¿Cómo arrancas el Servidor Web antes de que la Base de Datos esté lista?
Usando **Sockets**.
1.  Systemd crea el "enchufe" (socket) para la Base de Datos inmediatamente.
2.  Arranca el Servidor Web y la Base de Datos **simultáneamente**.
3.  El Servidor Web intenta conectar a la Base de Datos. Como el socket ya existe, la conexión se queda en espera (buffer) en lugar de fallar.
4.  Cuando la Base de Datos termina de arrancar, recoge las conexiones pendientes del socket y las procesa.

Resultado: Un sistema que arranca en 5 segundos en lugar de en 60.

@section: 2. El Átomo de Systemd: La Unidad (Unit)

En Systemd, no gestionamos "archivos" o "scripts". Gestionamos **Unidades**.
Cualquier recurso que Systemd sepa manejar se encapsula en un archivo de configuración llamado Unit File.

### Tipos de Unidades
El sistema reconoce el tipo de unidad por su extensión. Debes conocer las principales:

1.  **`.service`**: La más importante. Describe un proceso o aplicación (Nginx, Docker, SSH). Si quieres ejecutar un programa, usas un service.
2.  **`.socket`**: Un socket de comunicación (red o archivo IPC). Systemd puede escuchar en un puerto y arrancar un servicio solo cuando llega tráfico (Activación por Socket).
3.  **`.target`**: Un grupo lógico de unidades. Sirve para sincronizar el arranque. (Ej: `multi-user.target` es el grupo de servicios para el modo multiusuario).
4.  **`.timer`**: Un temporizador. Reemplaza a Cron. Activa un `.service` en una fecha/hora específica.
5.  **`.mount`**: Un punto de montaje del sistema de archivos. Reemplaza o complementa a `/etc/fstab`.
6.  **`.automount`**: Un punto de montaje que solo se monta cuando alguien intenta acceder a él.
7.  **`.device`**: Un dispositivo de hardware detectado por el kernel.
8.  **`.path`**: Permite monitorizar un archivo o directorio y activar un servicio si este cambia.

### Dónde Viven las Unidades
Hay una jerarquía estricta. Systemd busca los archivos en este orden. Si hay archivos con el mismo nombre, el de arriba gana (sobrescribe al de abajo).

1.  **`/etc/systemd/system/`**: **Prioridad ALTA**. Aquí es donde trabajas tú, el Administrador. Cualquier cosa que pongas aquí manda sobre el resto del sistema.
2.  **`/run/systemd/system/`**: **Prioridad MEDIA**. Unidades creadas dinámicamente en tiempo de ejecución. Se borran al reiniciar.
3.  **`/usr/lib/systemd/system/`**: **Prioridad BAJA**. Aquí es donde el gestor de paquetes (`apt`, `dnf`) instala las unidades por defecto.
    *   **REGLA DE ORO:** **JAMÁS** edites un archivo en `/usr/lib/...`. Si lo haces, la próxima vez que actualices el paquete (ej: `apt upgrade nginx`), el sistema sobrescribirá tu archivo y perderás tus cambios. Siempre debes copiarlo a `/etc/` o usar *overrides*.

@section: 3. Anatomía de un Servicio: Disección Forense

Vamos a abrir un archivo `.service` real y complejo para entender cada línea. Usaremos `sshd.service` (el servidor SSH) como paciente.

Para ver el contenido de la unidad que está cargada en memoria:
```bash
$ systemctl cat sshd.service
```

Verás algo parecido a esto (he añadido comentarios explicativos):

```ini
# SECCIÓN [Unit]: Metadatos y Dependencias
[Unit]
Description=OpenSSH Daemon
Documentation=man:sshd(8) man:sshd_config(5)

# Orden de arranque (Ordering)
# "Arranca después de que la red y el sistema de auditoría estén listos"
After=network.target auditd.service

# Dependencias (Dependencies)
# "Me gustaría que sshd-keygen.target también arranque, pero si falla, yo arranco igual"
Wants=sshd-keygen.target
# "NECESITO que sysinit.target haya terminado con éxito. Si falla, yo fallo".
Requires=sysinit.target

# SECCIÓN [Service]: Cómo ejecutar el proceso
[Service]
# Tipo de servicio (Vital entender esto)
# 'notify' significa que el servicio avisará activamente a systemd cuando esté listo.
Type=notify

# Archivo de variables de entorno (opcional)
EnvironmentFile=-/etc/default/ssh

# El comando principal. DEBE ser una ruta absoluta.
ExecStart=/usr/sbin/sshd -D $SSHD_OPTS

# Qué hacer si el administrador pide recargar (reload)
ExecReload=/bin/kill -HUP $MAINPID

# Qué hacer si el proceso muere
KillMode=process
Restart=on-failure
RestartSec=42s

# SECCIÓN [Install]: Cuándo activarse
[Install]
# "Actívame cuando el sistema llegue al nivel multi-usuario"
WantedBy=multi-user.target
```

### Profundizando en `[Unit]`
Aquí se define el lugar del servicio en el universo.

*   **`After=` / `Before=`**: Define solo **ORDEN**, no requerimiento.
    *   `After=network.target` significa: "Si la red y yo vamos a arrancar a la vez, ponme a mí en la cola después de la red". NO significa "Necesito la red". Si la red no arranca, SSH arrancará igual.
*   **`Requires=`**: Dependencia FUERTE. Si la unidad listada falla o no está activa, esta unidad fallará inmediatamente. Es muy estricto.
*   **`Wants=`**: Dependencia DÉBIL. Es la recomendada. Systemd intentará arrancar la otra unidad, pero si falla, no pasa nada. Tu servicio sigue.
*   **`BindsTo=`**: Si la otra unidad muere repentinamente, esta unidad también morirá. Útil para servicios vinculados a hardware (si desconecto la tarjeta de red, apaga el servicio de VPN).

### Profundizando en `[Service]`
Aquí está la carne.

*   **`Type=`**:
    *   `simple`: (Defecto). Systemd asume que el servicio está activo en el momento en que lanza el proceso. Rápido, pero si el proceso tarda en inicializarse, los dependientes podrían fallar.
    *   `forking`: Para demonios clásicos que se duplican en segundo plano (daemonize). Systemd espera a que el padre muera para considerar el servicio activo.
    *   `oneshot`: Para scripts que ejecutan una tarea y terminan (backup, firewall). El servicio se considera "activo" aunque el proceso haya terminado (si usas `RemainAfterExit=yes`).
    *   `notify`: El más avanzado. El servicio envía una señal a Systemd (`sd_notify`) para decir "¡Ya estoy listo!".
*   **`ExecStart=`**: El comando. No puedes usar tuberías `|`, redirecciones `>` o `&` aquí, porque no se ejecuta en una shell completa. Si necesitas eso, usa: `/bin/bash -c "comando | filtro > archivo"`.
*   **`User=` / `Group=`**: Con qué usuario corre el proceso. Por seguridad, nunca uses `root` a menos que sea imprescindible.
*   **`Restart=`**: La política de resurrección.
    *   `no`: Si muere, muere.
    *   `on-failure`: Si sale con error (código distinto de 0) o lo matan (kill), reinicia. Si se cierra limpiamente, no. (Recomendado para producción).
    *   `always`: Reinicia siempre, incluso si le dices que pare. (Cuidado con bucles infinitos).

### Profundizando en `[Install]`
Esta sección solo se lee cuando ejecutas `systemctl enable`.

*   **`WantedBy=`**: Dice "quién me quiere".
    *   Si pones `WantedBy=multi-user.target`, al habilitar el servicio, Systemd crea un enlace simbólico en `/etc/systemd/system/multi-user.target.wants/` apuntando a tu servicio.
    *   Cuando el sistema arranca y alcanza el objetivo `multi-user.target`, Systemd mira en esa carpeta `.wants/` y arranca todo lo que hay dentro. Así funciona el arranque automático.

@quiz: Estás escribiendo un servicio para un script de backup que debe ejecutarse y terminar, pero quieres que Systemd lo considere "activo" y exitoso una vez que el script finaliza. ¿Qué combinación usas?
@option: Type=simple
@correct: Type=oneshot con RemainAfterExit=yes
@option: Type=forking
@option: Type=notify

@section: 4. Controlando la Bestia: `systemctl`

Ya conoces `start`, `stop` y `restart`. Vamos a ver los comandos de un SysAdmin avanzado.

### Recargando sin parar (`reload`)
Muchos servicios (Apache, Nginx, SSH) saben leer su configuración de nuevo sin matar las conexiones activas.
```bash
$ sudo systemctl reload nginx
```
Úsalo siempre que cambies una configuración. `restart` es agresivo (mata y arranca), `reload` es suave. Si el servicio no soporta reload, systemctl te avisará.

### El estado real (`status`)
`systemctl status` te da muchísima información. Léela bien.
*   **Loaded:** ¿Está el archivo de unidad cargado? ¿Dónde está (`/lib/...` o `/etc/...`)? ¿Está `enabled` (arranque automático) o `disabled`?
*   **Active:**
    *   `active (running)`: Todo bien.
    *   `active (exited)`: Normal para servicios `oneshot`. Terminó bien.
    *   `inactive (dead)`: Parado.
    *   `failed`: Murió con error.
*   **Main PID:** El número de proceso principal.
*   **Logs:** Las últimas 10 líneas del log (journal) de ese servicio. Vital para ver por qué falló.

### Enmascarar (`mask`)
A veces, `disable` no es suficiente. Si deshabilitas un servicio, otro servicio podría despertarlo si tiene una dependencia `Wants=` sobre él.
Si quieres asegurar que un servicio **NUNCA** arranque bajo ninguna circunstancia (por ejemplo, para evitar conflictos graves o por seguridad):

```bash
$ sudo systemctl mask nginx
```
Esto crea un enlace simbólico de la unidad a `/dev/null`. Systemd pensará que la unidad está vacía. Cualquier intento de arrancarla (manual o automático) fallará. Para recuperarla: `unmask`.

@section: 5. Editando Unidades: El Método "Drop-In"

Imagina que quieres cambiar el usuario con el que corre un servicio, o añadir una variable de entorno.
**ERROR DE NOVATO:** Abrir `/usr/lib/systemd/system/servicio.service` con nano y editarlo.
Perderás los cambios en la próxima actualización.

**LA FORMA PRO:** Usar "Drop-In Overrides".
Systemd permite crear archivos que "parchean" la configuración original sin tocarla.

```bash
$ sudo systemctl edit nginx
```
Esto abrirá tu editor de texto y creará un archivo temporal. Todo lo que escribas ahí se guardará en un archivo especial `/etc/systemd/system/nginx.service.d/override.conf`.

Ejemplo: Queremos añadir un límite de memoria y cambiar la descripción.
```ini
[Unit]
Description=Nginx Servidor Web (Personalizado)

[Service]
MemoryMax=500M
```
Systemd fusionará esto con el archivo original. Tu configuración tiene prioridad.
Para ver la configuración final fusionada:
```bash
$ systemd-delta
# o
$ systemctl cat nginx
```

**Recargar el Demonio:**
Siempre que toques archivos de unidad en disco manualmente (sin usar `edit`), debes avisar a Systemd:
```bash
$ sudo systemctl daemon-reload
```
Si no lo haces, Systemd usará la versión en caché y te ignorará.

@section: 6. Targets: Los Niveles de Ejecución del Siglo XXI

En SysVinit había "Runlevels" (0 a 6). En Systemd hay **Targets**. Son más flexibles porque pueden heredarse y combinarse.

Los esenciales:
*   **`poweroff.target` (Runlevel 0):** Apagado.
*   **`rescue.target` (Runlevel 1):** Modo rescate. Sistema de archivos montado, pero sin red y con shell de root.
*   **`multi-user.target` (Runlevel 3):** El estándar para servidores. Red, multiusuario, sin entorno gráfico.
*   **`graphical.target` (Runlevel 5):** Modo escritorio. Igual que el anterior + Display Manager.
*   **`reboot.target` (Runlevel 6):** Reinicio.
*   **`emergency.target`:** Mínimo absoluto. Sistema de archivos en solo lectura. Úsalo si `rescue` falla.

### Cambiando de estado
Para pasar a modo mantenimiento (sin red, solo root) sin reiniciar:
```bash
$ sudo systemctl isolate rescue.target
```
*Cuidado: `isolate` para inmediatamente cualquier servicio que no sea dependencia del nuevo target. Si estás por SSH y te aíslas a un target sin red, te echará.*

### Cambiando el arranque por defecto
¿Tienes un servidor con Ubuntu Desktop pero quieres que arranque en modo texto para ahorrar RAM?
```bash
$ sudo systemctl set-default multi-user.target
```
En el próximo reinicio, no cargará la interfaz gráfica (Gnome/KDE), ahorrando 1GB de RAM.

@section: 7. Análisis de Rendimiento (Boot Profiling)

¿Tu servidor tarda 2 minutos en arrancar? Systemd sabe quién es el culpable.

### Visión General
```bash
$ systemd-analyze
Startup finished in 3.4s (kernel) + 12.1s (userspace) = 15.5s
```

### La Lista de la Vergüenza (`blame`)
Este comando ordena los servicios por el tiempo que tardaron en inicializarse.
```bash
$ systemd-analyze blame
8.2s network-online.target
4.1s docker.service
1.2s apt-daily.service
...
```
Si ves algo que tarda mucho y no lo necesitas, `disable` o `mask`.

### La Cadena Crítica (`critical-chain`)
A veces `blame` engaña. Un servicio puede tardar 5 segundos pero ejecutarse en paralelo sin molestar a nadie.
Lo que importa es la **Cadena Crítica**: la secuencia de servicios bloqueantes que retrasan el momento en que el sistema está 100% listo.
```bash
$ systemd-analyze critical-chain
```
Te mostrará un árbol en rojo con los culpables reales del retraso.

### Gráficos Vectoriales
Si quieres impresionar a tu jefe, genera un gráfico SVG de todo el proceso de arranque:
```bash
$ systemd-analyze plot > arranque.svg
```
Abre ese archivo con tu navegador y verás una línea de tiempo espectacular de cada proceso.

@section: 8. Cgroups: Controlando Recursos

Systemd usa **Cgroups** (Control Groups) del kernel para agrupar procesos. Esto le permite hacer algo increíble: **Limitar recursos por servicio**.

Imagina que tienes un script de backup (`backup.service`) que a veces se vuelve loco, usa el 100% de la CPU y cuelga tu base de datos.
Puedes ponerle una correa.

```bash
$ sudo systemctl edit backup.service
```

```ini
[Service]
# Límite duro de CPU (20% de un núcleo)
CPUQuota=20%
# Límite de memoria (si pasa de 1GB, el kernel lo mata)
MemoryMax=1G
# Prioridad de disco (baja)
IOWeight=10
```

Reinicia el servicio. Ahora, haga lo que haga ese script, **nunca** pasará del 20% de CPU. El kernel lo frenará. Es un salvavidas para entornos compartidos.

### Monitorizando con `systemd-cgtop`
Para ver el consumo de recursos agrupado por servicios (no por procesos individuales como `top`):
```bash
$ systemd-cgtop
```
Verás qué servicio (y todos sus hijos) está consumiendo más.

@section: 9. Systemd-run: Comandos Ad-Hoc

A veces quieres ejecutar un comando pesado (como una compilación o un `find` gigante) pero quieres las ventajas de Systemd (logs, límites de recursos, segundo plano) sin crear un archivo `.service`.
Usa `systemd-run`.

```bash
# Ejecutar un comando con nombre 'mi-tarea', límite de memoria y en segundo plano
$ sudo systemd-run --unit=mi-tarea --property=MemoryMax=500M /ruta/al/script.sh
Running as unit: mi-tarea.service
```

Ahora esa tarea corre gestionada por systemd.
*   Ver logs: `journalctl -u mi-tarea -f`
*   Parar: `systemctl stop mi-tarea`
*   Ver estado: `systemctl status mi-tarea`

Incluso si cierras tu terminal, la tarea sigue corriendo segura.

@section: 10. Laboratorio: El Servicio Inmortal

Vamos a crear un servicio en Python que sea resistente a fallos.

**Paso 1: El Script (`/usr/local/bin/inmortal.py`)**
```python
#!/usr/bin/env python3
import time
import sys

# Hacemos flush para que los logs salgan instantáneamente a systemd
print("El servicio Inmortal ha nacido...", flush=True)

try:
    while True:
        print("Sigo vivo...", flush=True)
        time.sleep(5)
except KeyboardInterrupt:
    print("Me están matando...", flush=True)
    sys.exit(0)
```
Dale permisos: `chmod +x /usr/local/bin/inmortal.py`

**Paso 2: La Unidad (`/etc/systemd/system/inmortal.service`)**
```ini
[Unit]
Description=Servicio de Prueba Inmortal
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/inmortal.py
# Política de reinicio: Si muere, revívelo.
Restart=always
# Espera 3 segundos antes de revivirlo (para no saturar la CPU si falla en bucle)
RestartSec=3

[Install]
WantedBy=multi-user.target
```

**Paso 3: Activación**
```bash
$ sudo systemctl daemon-reload
$ sudo systemctl start inmortal
$ sudo systemctl enable inmortal
```

**Paso 4: Prueba de Caos**
Vamos a asesinar al proceso para ver si Systemd cumple su promesa.
1.  Busca el PID: `systemctl status inmortal` (busca "Main PID").
2.  Mátalo: `sudo kill -9 [PID]`.
3.  Comprueba inmediatamente: `systemctl status inmortal`.

Verás que el estado es `active (running)`, pero el **PID ha cambiado**. Systemd detectó la muerte y lanzó una copia nueva en menos de 3 segundos.
Mira los logs: `journalctl -u inmortal`. Verás el momento de la muerte y la resurrección.

@section: Resumen / Cheat Sheet

| Comando | Acción |
| :--- | :--- |
| `systemctl daemon-reload` | **Obligatorio** después de editar cualquier archivo `.service`. |
| `systemctl edit [unit]` | Forma segura y persistente de modificar un servicio. |
| `systemctl mask [unit]` | Desactiva una unidad permanentemente (apunta a /dev/null). |
| `systemctl list-dependencies` | Muestra el árbol de requisitos. |
| `systemd-analyze blame` | Muestra qué servicios tardan más en arrancar. |
| `systemd-cgtop` | Monitor de recursos por servicio/cgroup. |
| `journalctl -xeu [unit]` | Ver logs detallados y errores de una unidad específica. |
| `systemd-run` | Ejecutar comandos ad-hoc como servicios temporales. |

Systemd tiene una curva de aprendizaje empinada, pero una vez que lo dominas, te da un control sobre el sistema operativo que los antiguos administradores de Unix solo podían soñar. Ahora eres el verdadero dueño del PID 1.