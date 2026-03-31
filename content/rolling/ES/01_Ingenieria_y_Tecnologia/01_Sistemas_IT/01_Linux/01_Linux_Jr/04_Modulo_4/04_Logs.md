@title: Registros del sistema: /var/log y journalctl
@icon: 📋
@description: Leer logs en texto y con systemd-journald; seguir eventos en vivo y relacionarlos con fallos de servicio o arranque.
@order: 4

# Logs en Linux: archivos en /var/log y journalctl

Bienvenido a la lección de medicina forense digital.

Cuando un servidor Windows falla, a menudo te muestra un código de error críptico (0x8004005) o, peor aún, una cara triste que dice "Algo salió mal". Te quedas a oscuras, sin saber qué ha pasado.

Cuando un servidor Linux falla, **te grita exactamente qué le duele, por qué le duele, dónde le duele y a qué hora empezó el dolor**.
El problema es que lo grita en un idioma que los novatos no saben leer: Los **Logs** (Registros).

Los Logs son el diario personal e íntimo del sistema operativo.
*   Cada vez que conectas un USB, el Kernel escribe una línea.
*   Cada vez que alguien intenta adivinar tu contraseña por SSH, el servicio de autenticación escribe una línea.
*   Cada vez que un programa se bloquea, el sistema escribe una línea.
*   Cada vez que envías un email, el servidor de correo escribe una línea.

Un Administrador de Sistemas que no sabe leer logs es como un médico que se niega a mirar las radiografías y prefiere adivinar la enfermedad mirando al paciente. Estás jugando a la lotería. Y en sistemas de producción, jugar a la lotería significa perder datos o ser hackeado.

En esta guía masiva, vamos a aprender a leer este diario. Vamos a ver los dos grandes sistemas de logging que conviven hoy en día (el clásico basado en archivos de texto y el moderno basado en bases de datos binarias), aprenderemos a ver el futuro en tiempo real con `tail -f` y a viajar al pasado con `journalctl`.

Prepárate. Vamos a buscar la verdad.

@section: 1. Los Dos Mundos del Logging

Linux está en una fase de transición tecnológica que dura ya más de una década. Actualmente, conviven dos filosofías de cómo guardar los registros. Para ser un buen administrador, tienes que dominar ambas, porque te encontrarás las dos.

### 1.1 El Método Clásico (Syslog / Archivos de Texto)
Esta es la forma tradicional de Unix, usada desde los años 70.
*   **Filosofía:** "Todo es un archivo de texto".
*   **Funcionamiento:** Un programa demonio (usualmente `rsyslog` o `syslog-ng`) escucha lo que dicen los programas y lo escribe en archivos de texto plano dentro de la carpeta `/var/log`.
*   **Ventaja:** Accesibilidad total. Puedes leerlos con cualquier editor de texto (`nano`, `cat`, `vim`). Si el sistema se rompe catastróficamente y no arranca, puedes sacar el disco duro, montarlo en otro PC y leer los archivos fácilmente con un simple editor de texto. Es a prueba de bombas.
*   **Desventaja:** Son archivos "tontos". No tienen índice. Buscar algo específico en un archivo de 2GB requiere usar herramientas como `grep` y expresiones regulares complejas. Además, rotarlos y borrarlos requiere gestión externa.

### 1.2 El Método Moderno (Systemd Journal / Binario)
Es el estándar actual en la mayoría de distribuciones modernas (Ubuntu, Fedora, CentOS, Debian reciente).
*   **Filosofía:** "Los logs son datos estructurados".
*   **Funcionamiento:** Los logs se guardan en una base de datos binaria optimizada y centralizada, gestionada por el demonio `systemd-journald`.
*   **Ventaja:** Potencia extrema. Está indexado. Puedes hacer consultas complejas como "dame los logs de ayer entre las 4:00 y las 5:00, pero solo los que sean errores críticos del servicio SSH". Te da la respuesta al instante. Además, es imposible de manipular fácilmente por un hacker (append-only).
*   **Desventaja:** No puedes leerlos con `cat` ni `nano`. Si intentas abrir el archivo, verás símbolos raros. Necesitas obligatoriamente una herramienta especial para leerlos: `journalctl`.

**La Realidad Híbrida:**
En la mayoría de los Linux que usarás hoy, **ambos sistemas funcionan a la vez**. Systemd recoge los logs primero y, por compatibilidad, también los reenvía al sistema clásico para que se escriban en los archivos de texto de siempre. Así tienes lo mejor de los dos mundos.

@section: 2. El Búnker de Papel: `/var/log`

Vamos a empezar por lo clásico, porque es lo que te salvará cuando todo lo demás falle. Abre una terminal y ve al corazón de las tinieblas:

```bash
$ cd /var/log
$ ls -l
```

Verás un montón de archivos. Aquí tienes la guía de campo de los más críticos. Memorízalos, porque serán tus mejores amigos.

### 2.1 Los Archivos Sagrados

#### 1. `/var/log/syslog` (Debian/Ubuntu) o `/var/log/messages` (RHEL/Fedora)
*   **El Cajón de Sastre.**
*   Aquí va casi todo lo que no tiene un archivo propio asignado. Información general del sistema, mensajes del kernel no críticos, errores de servicios genéricos, avisos de cron, etc.
*   **Cuándo mirarlo:** Es el primer sitio donde miras si "algo raro pasa" en el sistema pero no sabes qué componente está fallando. Si el sistema hizo algo extraño, probablemente dejó una nota aquí.

#### 2. `/var/log/auth.log` (Debian/Ubuntu) o `/var/log/secure` (RHEL/Fedora)
*   **El Registro de Seguridad.**
*   Este es vital. Contiene todos los eventos relacionados con la autenticación y la autorización.
*   **Contiene:**
    *   Intentos de login exitosos y fallidos (SSH, local, TTY).
    *   Uso del comando `sudo` (quién ejecutó qué comando como root).
    *   Creación y eliminación de usuarios o grupos.
    *   Cambios de contraseña.
*   **Cuándo mirarlo:** Si sospechas que te están hackeando, si has olvidado cuándo entró alguien, o si quieres auditar qué han hecho tus administradores con `sudo`.

#### 3. `/var/log/dmesg` o `/var/log/kern.log`
*   **La Voz del Kernel.**
*   Aunque `dmesg` es un comando (que veremos luego), a menudo su contenido se vuelca aquí.
*   **Contiene:** Todo lo relacionado con el hardware y el núcleo del sistema operativo. Detección de discos duros, inicialización de tarjetas de red, conexión de dispositivos USB, temperaturas críticas, errores de memoria RAM o fallos de segmentación.
*   **Cuándo mirarlo:** Si conectas un pendrive y no aparece, si la red no levanta al arrancar, o si el PC se reinicia solo por calor.

#### 4. `/var/log/apache2/` o `/var/log/nginx/` (Subcarpetas de Aplicación)
*   Las aplicaciones grandes y complejas (Servidores Web, Bases de Datos como MySQL, herramientas de Backup) suelen ser educadas y crean su propia subcarpeta para no ensuciar el `syslog` general.
*   Dentro de estas carpetas, es estándar encontrar dos archivos:
    *   **`access.log`**: Registro de todo lo que ha ido bien (quién visitó tu web, qué URL pidió, qué código 200 recibió).
    *   **`error.log`**: Registro de lo que ha ido mal (códigos 500, scripts de PHP que fallan, configuraciones rotas).

### 2.2 La Rotación de Logs (`logrotate`)
Si miras en tu carpeta `/var/log`, verás archivos con nombres raros como `syslog.1`, `syslog.2.gz`, `auth.log.2.gz`.
¿Qué son estos archivos?

Los logs crecen infinitamente. Un servidor ocupado puede generar 1GB de logs al día. Si nadie los parara, llenarían tu disco duro en unas semanas y el servidor colapsaría.
Linux usa un servicio ingenioso llamado **Logrotate** que se ejecuta automáticamente (usualmente cada noche mediante cron):

1.  **Renombrado:** Toma el archivo `syslog` actual y le cambia el nombre a `syslog.1`.
2.  **Reinicio:** Crea un archivo `syslog` nuevo y vacío para empezar a registrar los eventos de hoy.
3.  **Compresión:** Toma el `syslog.1` de ayer (que ahora es viejo) y lo comprime usando gzip para ahorrar espacio. Se convierte en `syslog.2.gz`.
4.  **Limpieza:** Borra el archivo más antiguo de la cadena (por ejemplo, `syslog.52.gz`) para liberar espacio en disco.

**Lección práctica:** Esto significa que `/var/log/syslog` contiene solo información **RECIENTE** (desde esta madrugada hasta ahora). Si buscas algo que pasó la semana pasada, no estará ahí. Tienes que mirar los archivos comprimidos `.gz` (usando herramientas como `zcat`, `zgrep` o `zless` que leen archivos comprimidos sin descomprimirlos).

@section: 3. Herramientas de Lectura: Por favor, no uses `cat`

Muchos novatos cometen el error de hacer:
```bash
$ cat /var/log/syslog
```
**ERROR.**
Ese archivo puede tener 500.000 líneas. Si haces `cat`:
1.  Tu terminal se inundará de texto pasando a velocidad Matrix.
2.  No podrás leer nada.
3.  Saturarás el buffer de tu terminal, perdiendo el historial anterior.
4.  Al final, solo verás las últimas 20 líneas y te habrás perdido el principio.

### 3.1 `less`: La forma civilizada
Para leer logs estáticos, usa siempre `less`. Es un paginador que te permite moverte.

```bash
$ sudo less /var/log/syslog
```
*(Nota: Necesitas `sudo` porque algunos logs, como `auth.log`, son privados y solo root puede leerlos por seguridad).*

**Navegación esencial en `less`:**
*   **Flechas:** Subir y bajar línea a línea.
*   **Espacio:** Bajar una página entera.
*   **b:** Subir una página entera (Back).
*   **G** (Mayúscula): Ir al final del archivo (lo más reciente).
*   **g** (Minúscula): Ir al principio del archivo (lo más antiguo del día).
*   **/**: Buscar texto. Escribe `/error` y pulsa Enter. Pulsa `n` para ir a la siguiente coincidencia.
*   **q**: Salir (Quit).

### 3.2 `tail`: El bisturí
A menudo no te importa lo que pasó a las 3 de la mañana. Solo te importa lo que pasó hace 5 minutos.
`tail` (cola) muestra el final del archivo.

```bash
# Ver las últimas 10 líneas (comportamiento por defecto)
$ sudo tail /var/log/syslog

# Ver las últimas 50 líneas
$ sudo tail -n 50 /var/log/syslog
```

@section: 4. El Superpoder: Monitorización en Tiempo Real (`tail -f`)

Imagina esta situación típica:
Estás configurando un servidor web. Intentas arrancarlo y falla.
Vas al log, lo abres con `less`, vas al final, lees el error. Sales. Intentas arreglar la configuración. Vuelves a intentar arrancar. Vuelves a abrir el log con `less`, vas al final...
Es lento. Es tedioso.

Lo que hacen los profesionales es **ver el log en directo, como una película, mientras ocurren las cosas**.

El comando mágico es:
```bash
$ sudo tail -f /var/log/syslog
```
*(`-f` significa **Follow** / Seguir).*

**Cómo funciona:**
1.  El comando muestra las últimas 10 líneas del archivo.
2.  **No se cierra.** El programa se queda esperando, "enganchado" al archivo, con el cursor parpadeando.
3.  En el momento exacto en que el sistema (o un servicio) escribe una nueva línea al final del archivo, `tail` la detecta y la imprime en tu pantalla inmediatamente.

**Ejercicio Mental de Diagnóstico:**
1.  Abres una terminal y ejecutas `sudo tail -f /var/log/auth.log`. La dejas en una esquina de la pantalla.
2.  Abres OTRA terminal (o usas otro PC) e intentas loguearte por SSH fallando la contraseña a propósito.
3.  Miras la primera terminal. ¡Verás el error de autenticación aparecer instantáneamente en tu pantalla! "Failed password for user..."

Esto es fundamental para el diagnóstico ("Troubleshooting"). Te permite correlacionar **tu acción** (intentar entrar) con la **reacción del sistema** (el log) en tiempo real.

Para salir del modo `-f` y recuperar tu terminal, pulsa **Ctrl + C**.

@section: 5. La Base de Datos Moderna: `journalctl`

Ahora que dominas los archivos de texto, pasemos al futuro: **Systemd Journal**.
Aquí no lidiamos con archivos sueltos. No tienes que saber dónde se guardan. Usamos una herramienta de consulta poderosa: **`journalctl`**.

Si ejecutas `journalctl` a secas, verás TODOS los logs que el sistema tiene guardados desde el principio de los tiempos. Es una manguera de información inmanejable. El truco para usar Journal es saber **filtrar**.

### 5.1 Filtrado por Tiempo
"¿Qué pasó ayer por la mañana?"
Con archivos de texto, tendrías que buscar qué archivo `.gz` corresponde a ayer, descomprimirlo y buscar la hora.
Con `journalctl` es lenguaje natural:

```bash
# Logs desde ayer
$ journalctl --since "yesterday"

# Logs desde hace 1 hora
$ journalctl --since "1 hour ago"

# Un intervalo concreto
$ journalctl --since "2023-10-20 14:00:00" --until "2023-10-20 15:00:00"
```

### 5.2 Filtrado por Servicio (`-u`)
Esta es la opción más usada. Solo quieres ver qué dice el servidor web (Nginx) o el servidor SSH. No te interesan los mensajes del kernel ni del cron.
En systemd, los servicios se llaman "Units". Por eso la opción es `-u`.

```bash
# Ver solo logs de SSH
$ journalctl -u ssh

# Ver solo logs de Docker
$ journalctl -u docker

# Ver logs de Nginx desde hoy
$ journalctl -u nginx --since "today"
```

### 5.3 Filtrado por Arranque (`-b`)
A veces reinicias el PC porque algo iba mal, y quieres ver qué pasó en la sesión anterior (antes de reiniciar), justo antes del cuelgue.
Los logs de texto tradicionales suelen mezclarse o rotarse de forma confusa. `journalctl` entiende perfectamente el concepto de "Boot" (Arranque).

```bash
# Logs del arranque actual (desde que encendiste hoy hasta ahora)
$ journalctl -b

# Logs del arranque ANTERIOR (desde que encendiste ayer hasta que apagaste/reiniciaste)
$ journalctl -b -1

# Logs del arranque de hace dos veces
$ journalctl -b -2
```
Esto es magia negra para diagnosticar cuelgues del sistema. Si tu PC se congeló ayer, haz `journalctl -b -1` y ve al final (`G`) para ver las últimas palabras del sistema antes de morir (el "testamento" del kernel).

### 5.4 Modo Tiempo Real (`-f`)
Al igual que `tail`, `journalctl` tiene modo follow. Es la forma moderna de monitorizar.

```bash
# Seguir logs de SSH en tiempo real
$ journalctl -u ssh -f
```
Verás los logs coloreados y formateados. Es mucho más legible que `tail`.

### 5.5 Otros filtros útiles
*   **Solo Errores (`-p`):** Muestra solo mensajes de prioridad "Error", "Critical" o "Alert". Elimina todo el ruido informativo ("Info" o "Debug"). Ideal para ver qué está roto rápidamente.
    ```bash
    $ journalctl -p err
    ```
*   **Kernel (`-k`):** Muestra solo mensajes del Kernel (equivalente al comando `dmesg`).
    ```bash
    $ journalctl -k
    ```
*   **Formato JSON (`-o json`):** Si eres programador y quieres procesar los logs con un script de Python o enviarlos a un sistema de análisis (como ELK stack), puedes pedirlos en JSON.
    ```bash
    $ journalctl -u ssh -o json-pretty
    ```

@section: 6. `dmesg`: El Susurro del Hardware

Existe un comando específico y antiguo para leer el "Kernel Ring Buffer". Es una zona de memoria circular donde el Kernel escribe mensajes sobre hardware y drivers incluso antes de que los discos duros estén montados y el sistema de logging normal haya arrancado.

```bash
$ sudo dmesg
```
Normalmente usarás `dmesg` con tuberías (`| less`) o con la opción `-H` para verlo paginado y con colores:
```bash
$ sudo dmesg -H
```

**Caso de Uso Práctico: El USB fantasma**
Conectas un pendrive USB y no pasa nada. No aparece en el escritorio. ¿Está roto el USB? ¿Está roto el puerto? ¿Falta el driver?
1.  Abre una terminal.
2.  Ejecuta `sudo dmesg -w` (modo wait/follow, igual que `-f`).
3.  Desconecta el USB.
4.  Conecta el USB.
5.  Mira la pantalla. Verás líneas nuevas aparecer en tiempo real.
    *   Si dice `New USB device found... sdb: sdb1`, el hardware funciona, el puerto funciona y el sistema lo ve. El problema es de software (no se ha montado automáticamete).
    *   Si dice `USB error`, `device descriptor read/64, error -110` o `I/O error`, tu pendrive está físicamente dañado o el puerto está mal.
    *   Si no dice NADA absoluto, tu puerto USB está muerto eléctricamente.

@section: 7. Laboratorio Práctico: El Detective

Vamos a simular un problema y a encontrarlo usando logs. Sigue estos pasos en tu terminal.

**Escenario:** Un usuario se queja de que no puede usar `sudo` para ser administrador. Dice que el sistema le odia.

1.  **Reproducir el problema:**
    Intenta hacer algo con sudo y falla la contraseña a propósito (o usa un usuario que no tenga permisos).
    ```bash
    $ sudo ls /root
    [sudo] password for alumno: (escribe mal la contraseña)
    Sorry, try again.
    (Falla 3 veces)
    sudo: 3 incorrect password attempts
    ```

2.  **Buscar la evidencia (Método Clásico):**
    Vamos a buscar en `auth.log`, que registra estos eventos.
    ```bash
    $ sudo tail /var/log/auth.log
    ```
    Verás líneas recientes como: `FAILED su for root... authentication failure` o `sudo: ... : 3 incorrect password attempts`. Ahí está la prueba. El usuario falló la contraseña.

3.  **Buscar la evidencia (Método Moderno):**
    Usa journalctl buscando el ejecutable de sudo.
    ```bash
    $ journalctl /usr/bin/sudo | tail
    ```
    Systemd es inteligente y permite buscar por la ruta del programa.

4.  **Monitorización Activa (La Trampa):**
    Abre una terminal y déjala corriendo con:
    ```bash
    $ journalctl -f
    ```
    Ahora, en otra ventana, usa `sudo` correctamente (escribe bien la contraseña).
    Verás en la primera terminal aparecer una línea similar a:
    `COMMAND=/usr/bin/ls /root`
    Sudo registra cada comando ejecutado exitosamente. Ahora sabes que el sistema funciona bien y registra tanto los éxitos como los fracasos.

@section: 8. Mantenimiento del Journal

El sistema de logs binario (`journalctl`) puede crecer mucho si no se controla. A diferencia de `logrotate`, que borra archivos antiguos, `journalctl` gestiona su propio espacio en disco.

Ver cuánto ocupan tus logs actualmente:
```bash
$ journalctl --disk-usage
Archived and active journals take up 1.2G in the file system.
```

Si crees que 1.2GB es demasiado para logs, puedes hacer una limpieza manual (Vacuum):

```bash
# Borrar todo excepto los logs de los últimos 2 días
$ sudo journalctl --vacuum-time=2d

# Borrar logs antiguos hasta que el total ocupe solo 500MB
$ sudo journalctl --vacuum-size=500M
```
Esto es útil en servidores con poco espacio en disco.

@section: Resumen / Cheat Sheet

Guarda esta tabla cerca. Te salvará la vida.

| Objetivo | Comando Clásico (Archivos) | Comando Moderno (Systemd) |
| :--- | :--- | :--- |
| **Ver todo el log (paginado)** | `less /var/log/syslog` | `journalctl` |
| **Ver el final (últimas líneas)** | `tail /var/log/syslog` | `journalctl -e` |
| **Monitorizar en Tiempo Real** | `tail -f /var/log/syslog` | `journalctl -f` |
| **Filtrar por Servicio (SSH)** | `grep ssh /var/log/auth.log` | `journalctl -u ssh` |
| **Ver solo Errores** | `grep "Error" /var/log/syslog` | `journalctl -p err` |
| **Ver logs del Boot Anterior** | (Difícil, buscar en archivos .gz) | `journalctl -b -1` |
| **Ver mensajes del Kernel**| `dmesg` | `journalctl -k` |

**Consejo Final de Sabiduría:**
Cuando algo no funcione en Linux, **NO REINICIES** como harías en Windows.
Reiniciar borra las pistas del crimen (especialmente las de `dmesg` y los estados temporales).
1.  Abre los logs.
2.  Lee las últimas líneas.
3.  Copia el mensaje de error exacto.
4.  Pégalo en Google.
El log es la diferencia entre decir "No funciona" y decir "Sé cómo arreglarlo".