
@title: Kernel: módulos, sysctl y compilación desde fuentes
@icon: 🔧
@description: Cargar y descargar módulos, ajustes en caliente con sysctl, tainting y pasos básicos para compilar e instalar un kernel a medida.
@order: 2

# Kernel Linux: módulos, parámetros en tiempo de ejecución y compilación

Bienvenido al Santo Grial de Linux.

Hasta ahora, el Kernel (núcleo) ha sido una "caja negra" para ti. Un archivo llamado `vmlinuz` que carga al principio, escupe algunas letras rápidas y hace magia para que tu ratón se mueva y tu WiFi conecte. Pero un SysAdmin avanzado no cree en la magia. Cree en el código C y en la gestión de recursos.

Un SysAdmin avanzado debe saber mirar dentro de esa caja, ajustarla mientras el motor está en marcha y, si es necesario, construir una nueva pieza a medida en la herrería.

En este módulo masivo, vamos a desmitificar el componente más importante de tu sistema operativo. No solo aprenderemos a compilar; aprenderemos a pensar como el Kernel.

@section: 1. Anatomía del Kernel: Monolítico pero Flexible

Antes de tocar nada, necesitamos entender qué estamos tocando.

### ¿Qué es exactamente el Kernel?
Imagina una orquesta sinfónica.
*   **El Hardware** son los instrumentos (violines, trompetas, timbales).
*   **Las Aplicaciones** son los músicos (quieren tocar notas).
*   **El Kernel** es el **Director de Orquesta**.

Sin el director, todos los músicos intentarían tocar a la vez, peleándose por el mismo espacio, y el resultado sería ruido. El Kernel decide:
1.  Quién toca ahora (Gestión de CPU / Scheduler).
2.  Dónde se sienta cada uno (Gestión de Memoria).
3.  Cómo se comunican con los instrumentos (Drivers / Controladores).

### La Arquitectura: Monolítico Modular
Existen dos grandes filosofías de diseño de Kernels:

1.  **Microkernel:** El núcleo es diminuto. Solo maneja lo básico (memoria y CPU). Los drivers (tarjeta gráfica, disco duro) son procesos separados que corren "fuera" del núcleo.
    *   *Ventaja:* Si el driver de la gráfica falla, el sistema no se cuelga, solo reinicia el driver.
    *   *Desventaja:* La comunicación es más lenta.
    *   *Ejemplo:* MINIX, GNU Hurd.

2.  **Kernel Monolítico:** El núcleo es gigante. Todo (drivers, sistema de archivos, red) vive dentro del mismo espacio de memoria y se ejecuta con privilegios absolutos.
    *   *Ventaja:* Velocidad extrema. Todo está ahí mismo.
    *   *Desventaja:* Si un driver falla (ej: driver de una impresora china barata), puede tumbar todo el sistema (Kernel Panic).
    *   *Ejemplo:* **Linux**.

**Espera, ¿Linux es monolítico?**
Sí, pero con un truco genial: es **Modular**.
Aunque es un solo bloque, permite cargar y descargar piezas de código (Drivers) en caliente, sin reiniciar. Estas piezas se llaman **Módulos del Kernel (.ko)**.

Esto nos da lo mejor de los dos mundos: la velocidad de un monolito y la flexibilidad de poder enchufar hardware nuevo (un USB, una cámara) sin tener que recompilar y reiniciar el ordenador entero.

@section: 2. Gestión de Módulos: Mecánica en Caliente

La mayoría de los drivers en tu sistema Linux no están "soldados" dentro del archivo `vmlinuz`. Son archivos externos que viven en el disco duro, normalmente en `/lib/modules/$(uname -r)/`.

El Kernel carga estos archivos en la memoria RAM solo cuando los necesita. Esto mantiene el Kernel ligero y rápido.

### Explorando los Módulos Cargados (`lsmod`)
¿Qué tiene tu Kernel cargado ahora mismo? Vamos a verlo.

```bash
$ lsmod
```

Verás una tabla con tres columnas:
1.  **Module:** El nombre del módulo (ej: `iwlwifi`, `nouveau`, `ext4`).
2.  **Size:** Cuánto ocupa en memoria (en bytes).
3.  **Used by:** Un contador y una lista.
    *   El número indica cuántos procesos u otros módulos están usando este módulo.
    *   La lista dice *quiénes* son.

**Ejemplo:**
```text
Module                  Size  Used by
iptable_filter         12345  1
ip_tables              23456  1 iptable_filter
nouveau               987654  3
video                  45678  1 nouveau
```
Aquí vemos que `video` está siendo usado por `nouveau` (el driver gráfico). Esto significa que **no podemos descargar** el módulo `video` mientras `nouveau` esté activo. Existe una dependencia.

### Información Forense (`modinfo`)
Ves un módulo llamado `e1000e` y no sabes qué es. Interrógalo.

```bash
$ modinfo e1000e
```

La salida es oro puro:
*   **filename:** Dónde está el archivo `.ko` en el disco.
*   **description:** "Intel(R) PRO/1000 Network Driver". ¡Ah, es la tarjeta de red!
*   **author:** Quién lo escribió (Intel Corporation).
*   **license:** GPL.
*   **depends:** De qué otros módulos depende.
*   **parm:** (Parámetros). Esta es la parte más útil para tuning. Te dice qué opciones puedes pasarle al módulo para cambiar su comportamiento.
    *   Ejemplo: `parm: debug:Debug level (0=none,..., 16=all) (int)`

### Cargando Módulos (`modprobe` vs `insmod`)
Hay dos formas de cargar un módulo, la tonta y la lista.

1.  **La forma tonta (`insmod`):**
    `sudo insmod /ruta/al/modulo.ko`
    *   Es "tonta" porque no resuelve dependencias. Si el módulo A necesita al B, y cargas A, `insmod` fallará y te dirá "Símbolo desconocido". Solo se usa en desarrollo.

2.  **La forma lista (`modprobe`):**
    `sudo modprobe nombre_modulo`
    *   Es inteligente. Consulta una base de datos de dependencias (`modules.dep`). Si le pides cargar A, él ve que necesita B, así que carga B primero y luego A. **Usa siempre `modprobe`.**

**Ejemplo práctico: Cargar soporte para Btrfs**
```bash
$ sudo modprobe btrfs
```
Si no hay errores, el comando no dice nada (silencio unix). Ahora tu Kernel sabe hablar el idioma del sistema de archivos Btrfs.

### Descargando Módulos (`modprobe -r`)
Para quitar un driver (por ejemplo, si se ha quedado colgado o quieres actualizarlo):

```bash
$ sudo modprobe -r btrfs
```
*(`-r` significa Remove).*

**Error común:** `modprobe: FATAL: Module btrfs is in use.`
Esto ocurre porque intentas quitar el driver mientras tienes un disco Btrfs montado. El Kernel te protege de tu propia estupidez. Desmonta el disco primero (`umount`) y luego descarga el módulo.

@quiz: Estás intentando descargar un módulo con `modprobe -r` pero obtienes un error diciendo que está en uso. ¿Qué comando te ayudaría a identificar qué otros módulos o procesos lo están usando?
@option: modinfo
@correct: lsmod
@option: insmod
@option: dmesg

@section: 3. Configuración Persistente y Blacklisting

Los cambios que haces con `modprobe` son **volátiles**. Si reinicias el ordenador, el Kernel vuelve a su estado original y carga solo lo que detecta automáticamente.

¿Cómo hacemos cambios permanentes? Usando los archivos de configuración en `/etc`.

### Cargar módulos al inicio (`/etc/modules`)
Si tienes un módulo que el sistema no detecta automáticamente (raro hoy en día, pero pasa con hardware custom), puedes forzar su carga.
Edita `/etc/modules` (o crea un archivo en `/etc/modules-load.d/`).
Simplemente escribe el nombre del módulo en una línea.

### Pasar parámetros (`/etc/modprobe.d/`)
Imagina que tu tarjeta WiFi (`iwlwifi`) es inestable y has leído en un foro que desactivando el modo 11n se arregla.
`modinfo iwlwifi` te dice que tiene un parámetro `11n_disable`.

Creas un archivo `/etc/modprobe.d/wifi.conf`:
```text
options iwlwifi 11n_disable=1
```
En el próximo reinicio, el Kernel aplicará esa opción automáticamente.

### La Lista Negra (Blacklisting)
A veces, el Kernel carga un driver que **NO** quieres.
El caso más famoso: Quieres instalar los drivers propietarios de NVIDIA. Pero Linux, por defecto, carga el driver libre `nouveau`. Ambos drivers pelean por la tarjeta gráfica y el sistema explota.
Necesitas decirle al Kernel: "Bajo ningún concepto cargues nouveau".

Creas `/etc/modprobe.d/blacklist-nvidia.conf`:
```text
blacklist nouveau
options nouveau modeset=0
```

**¡PASO CRÍTICO! Regenerar el Initramfs**
Muchos módulos (como los de disco o gráfica) se cargan *antes* de que el Kernel pueda leer tu disco duro principal. Se cargan desde el `initramfs` (la mochila de arranque).
Si editas `/etc/modprobe.d/`, solo cambias el disco duro, pero la mochila sigue teniendo el driver viejo.
Debes actualizar la mochila:

*   **Debian/Ubuntu:** `sudo update-initramfs -u`
*   **RedHat/Fedora:** `sudo dracut --force`

@quiz: Acabas de crear un archivo en `/etc/modprobe.d/` para poner en blacklist un driver de vídeo que causa conflictos durante el arranque. Reinicias, pero el driver se sigue cargando. ¿Qué paso olvidaste?
@option: Dar permisos de ejecución al archivo .conf.
@option: Ejecutar el comando `sysctl -p`.
@correct: Regenerar el initramfs (con `update-initramfs` o `dracut`) para que la configuración se incluya en la imagen de arranque inicial.
@option: Reiniciar el servicio systemd-modules-load.

@section: 4. Ajuste del Kernel en Tiempo Real (`sysctl`)

El Kernel no es estático. Es un organismo vivo con miles de variables que controlan su comportamiento.
¿Cuánta memoria usa para caché? ¿Cómo gestiona los paquetes de red? ¿Debe reiniciar si se cuelga?

Estas "perillas" de ajuste están expuestas a través del sistema de archivos virtual **`/proc`**.
Específicamente en **`/proc/sys/`**.

### Explorando el cerebro del Kernel
Entra ahí.
```bash
$ cd /proc/sys
$ ls
abi  debug  dev  fs  kernel  net  vm ...
```
Todo lo que ves parecen archivos, pero no están en el disco duro. Son ventanas directas a la memoria del Kernel.

*   **`net/`**: Configuración de red (IPv4, IPv6, Core).
*   **`vm/`**: Memoria virtual (RAM, Swap, Caché).
*   **`kernel/`**: Configuraciones del núcleo (Pánico, Hostname, PIDs).
*   **`fs/`**: Sistema de archivos (Límites de archivos abiertos).

Si haces `cat /proc/sys/vm/swappiness`, verás un número (ej: `60`).
Si haces `echo 10 > /proc/sys/vm/swappiness` (como root), ¡has cambiado el comportamiento de la gestión de memoria del sistema operativo instantáneamente!

### La Herramienta `sysctl`
Aunque usar `echo` funciona, la forma profesional y segura es el comando `sysctl`.

**Leer un valor:**
```bash
$ sysctl vm.swappiness
vm.swappiness = 60
```

**Escribir un valor (Temporal):**
Queremos que el sistema use menos swap para mejorar el rendimiento en escritorio.
```bash
$ sudo sysctl -w vm.swappiness=10
```
El cambio es inmediato. No hace falta reiniciar servicios. Pero si reinicias el PC, se perderá.

### Hacerlo Persistente (`/etc/sysctl.conf`)
Para que los cambios sobrevivan al reinicio, debes escribirlos en `/etc/sysctl.conf` o en un archivo dentro de `/etc/sysctl.d/`.

**Ejemplo de configuración de hardening (seguridad):**
```ini
# /etc/sysctl.d/99-seguridad.conf

# Ignorar Pings (ICMP Echo Request) - Modo Sigilo
net.ipv4.icmp_echo_ignore_all = 1

# Deshabilitar el reenvío de paquetes (Para que no usen tu PC como router)
net.ipv4.ip_forward = 0

# Protección contra ataques SYN Flood
net.ipv4.tcp_syncookies = 1

# Deshabilitar IPv6 si no lo usas (reduce superficie de ataque)
net.ipv6.conf.all.disable_ipv6 = 1
```

Para aplicar estos cambios sin reiniciar:
```bash
$ sudo sysctl -p /etc/sysctl.d/99-seguridad.conf
```

@quiz: Quieres que tu servidor Linux deje de responder a los comandos `ping` para ser menos visible en la red. ¿Qué parámetro de `sysctl` debes modificar?
@option: net.ipv4.ip_forward
@option: vm.swappiness
@correct: net.ipv4.icmp_echo_ignore_all
@option: net.ipv4.tcp_syncookies

@section: 5. Kernel Tainting (La Mancha)

A veces, al mirar los logs del sistema (`dmesg` o `journalctl -k`), verás un mensaje preocupante al principio:
`Kernel tainted` (Kernel manchado).

¿Está tu sistema infectado? ¿Está roto?
No necesariamente.

**La Filosofía GPL y la Pureza:**
El Kernel Linux es Software Libre (GPL). Los desarrolladores del Kernel solo pueden garantizar la estabilidad y depurar errores si tienen acceso a *todo* el código fuente que se está ejecutando.
Si cargas un módulo **Propietario** (Código Cerrado), como el driver de NVIDIA (`nvidia.ko`) o algunos drivers WiFi privativos, el Kernel detecta que ha entrado "código secreto" en su espacio de memoria.

En ese momento, el Kernel levanta una bandera: **"Estoy Manchado (Tainted)"**.

**Consecuencias:**
1.  **Funcionalidad:** Ninguna. Tu sistema funcionará perfectamente (o incluso mejor, si necesitabas ese driver para jugar).
2.  **Soporte:** Si tu sistema se cuelga (Kernel Panic) y envías el reporte de error a la comunidad de desarrolladores del Kernel, verán la bandera "Tainted". Automáticamente rechazarán tu reporte. Te dirán: *"No podemos saber si el fallo es nuestro o del driver secreto de NVIDIA. Reproduce el error sin el driver manchado y vuelve a avisarnos"*.

Es una marca de "Garantía Anulada" para los desarrolladores open source.

@section: 6. El Rito de Iniciación: Compilar tu Propio Kernel

Llegamos a la prueba de fuego. El cinturón negro del SysAdmin.
¿Por qué querrías compilar tu propio kernel en 2025, cuando las distros ya te dan uno perfecto?

**Razones para compilar:**
1.  **Hardware Bleeding Edge:** Tu portátil es tan nuevo que el driver del touchpad solo está en la versión 6.9 del Kernel, pero tu Ubuntu usa la 6.5.
2.  **Optimización Extrema:** Quieres un Kernel para un sistema embebido (IoT) que ocupe 2MB en lugar de 100MB. Quitas el soporte para todo lo que no sea tu hardware exacto.
3.  **Seguridad y Parches:** Necesitas aplicar un parche de seguridad crítico (zero-day) que ha salido hoy en el código fuente, pero tu distro tardará una semana en empaquetarlo.
4.  **Aprendizaje:** Porque no entiendes Linux hasta que construyes Linux.

**ADVERTENCIA:** Si fallas en este proceso, tu sistema puede dejar de arrancar. Asegúrate de tener siempre un Kernel antiguo funcional en el menú de GRUB para poder volver atrás. Nunca borres el kernel viejo hasta que el nuevo lleve una semana funcionando bien.

### Paso 1: Preparar el Terreno
Necesitas herramientas de compilación. Son un montón de librerías para procesar código C, ensamblador y certificados.

En Debian/Ubuntu:
```bash
$ sudo apt update
$ sudo apt install build-essential libncurses-dev bison flex libssl-dev libelf-dev
```

### Paso 2: Obtener las Fuentes (El Código)
Ve a **kernel.org**. Es la fuente oficial. Verás la última versión estable (digamos 6.8.1).
Descarga el "Tarball" (.tar.xz).

```bash
# Vamos a trabajar en /usr/src, el lugar estándar para fuentes
$ cd /usr/src
$ sudo wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.8.1.tar.xz
$ sudo tar -xvf linux-6.8.1.tar.xz
$ cd linux-6.8.1
```

### Paso 3: La Configuración (`.config`)
El Kernel tiene más de 10.000 opciones configurables.
*   ¿Soporte para radioaficionados?
*   ¿Sistema de archivos Apple Macintosh antiguo?
*   ¿Drivers para joysticks de los 90?

Si intentas configurarlo todo desde cero, tardarás días y fallarás.
La estrategia inteligente es: **Copiar la configuración de tu distro actual**.

```bash
$ cp /boot/config-$(uname -r) .config
```
Esto copia el archivo de configuración de tu kernel actual a la carpeta de fuentes.

Ahora, abrimos el menú de configuración gráfica (basado en texto):
```bash
$ make menuconfig
```
Aparecerá una pantalla azul estilo BIOS. Aquí puedes navegar y activar/desactivar características.
*   **`[*]` (Asterisco):** Integrado (Built-in). El código va dentro del archivo `vmlinuz`. Siempre activo. Aumenta el tamaño del arranque. (Obligatorio para el driver del disco duro y el sistema de archivos raíz).
*   **`[M]` (Módulo):** Se compila como archivo `.ko` externo. Se carga solo si hace falta. (Recomendado para casi todo lo demás).
*   **`[ ]` (Vacío):** Excluido. No se compila.

*Tip:* Si solo quieres actualizar el kernel sin cambiar opciones, simplemente sal y guarda.

### Paso 4: La Compilación
Este es el momento de la verdad. Vas a convertir millones de líneas de código C en código máquina.
Dependiendo de tu CPU, esto puede tardar desde 10 minutos (en un Threadripper de 32 núcleos) hasta 5 horas (en una Raspberry Pi).

Usamos el flag `-j` para decirle a `make` cuántos núcleos usar. Una buena regla es `nproc` (número de procesadores).

```bash
$ make -j$(nproc)
```
Verás la pantalla inundarse de texto. Siéntate y disfruta. Es tu ordenador trabajando al 100%. Si termina sin decir "Error", has triunfado.

### Paso 5: Instalación de Módulos
El paso anterior creó el `vmlinuz` y miles de archivos `.ko`. Ahora hay que copiarlos a su sitio.
Primero los módulos:
```bash
$ sudo make modules_install
```
Esto los copiará a `/lib/modules/6.8.1/`.

### Paso 6: Instalación del Kernel
Ahora instalamos el núcleo.
```bash
$ sudo make install
```
Este comando hace magia negra scriptada por tu distribución:
1.  Copia `vmlinuz` a `/boot`.
2.  Copia `System.map` (tabla de símbolos para depuración) a `/boot`.
3.  Copia el `.config` a `/boot`.
4.  **Genera automáticamente el initramfs** para tu nuevo kernel.
5.  **Actualiza el GRUB** para añadir la nueva entrada automáticamente.

### Paso 7: El Reinicio
Cruza los dedos.
```bash
$ sudo reboot
```
En el menú de GRUB, selecciona "Advanced Options" si no sale por defecto, o simplemente deja que arranque. Deberías ver tu nueva versión "Linux 6.8.1".

Si arranca, abre una terminal y escribe `uname -r`.
Si ves `6.8.1`... **¡Felicidades! Has compilado el motor de tu coche y lo estás conduciendo.**

@section: 7. DKMS: El Salvador de las Actualizaciones

Compilar el kernel entero es divertido, pero ¿qué pasa cuando solo quieres instalar el driver de tu tarjeta WiFi nueva que no viene en el kernel?
Normalmente te bajas el código del driver y lo compilas.
Pero, **¡problema!**
Ese driver se compila "contra" tu versión actual del kernel.
Si la semana que viene haces `apt upgrade` y se actualiza el kernel del sistema, tu driver WiFi dejará de funcionar porque fue compilado para el kernel viejo. Tendrías que recompilarlo manualmente cada vez que actualizas. Un infierno.

**La Solución: DKMS (Dynamic Kernel Module Support)**
DKMS es un sistema que automatiza esto.
Cuando instalas un driver con soporte DKMS (como los de NVIDIA o VirtualBox):
1.  El código fuente del driver se guarda en `/usr/src`.
2.  DKMS instala un "gancho" (hook) en el sistema de actualización del kernel.
3.  Cuando instalas un Kernel nuevo, DKMS lo detecta y **automáticamente recompila tu driver para el nuevo kernel** antes de que reinicies.

Gracias a DKMS, puedes tener drivers externos y actualizar tu kernel sin miedo a perder la gráfica o el WiFi.

@section: Resumen / Cheat Sheet

| Comando / Archivo | Función |
| :--- | :--- |
| `lsmod` | Listar módulos cargados actualmente. |
| `modinfo [mod]` | Ver detalles, autor y parámetros de un módulo. |
| `modprobe [mod]` | Cargar un módulo resolviendo dependencias. |
| `modprobe -r [mod]` | Descargar un módulo. |
| `/etc/modprobe.d/` | Carpeta para configuración persistente (opciones, blacklist). |
| `sysctl -a` | Ver todos los parámetros del kernel en tiempo real. |
| `sysctl -w var=val` | Cambiar un parámetro temporalmente. |
| `/etc/sysctl.conf` | Cambios de sysctl persistentes. |
| `make menuconfig` | Menú gráfico para configurar la compilación del kernel. |
| `uname -r` | Ver la versión del kernel que estás ejecutando. |

El Kernel ya no es un misterio para ti. Es un programa. Un programa complejo, vital y fascinante, pero un programa que tú, como administrador, puedes controlar, ajustar y reconstruir.