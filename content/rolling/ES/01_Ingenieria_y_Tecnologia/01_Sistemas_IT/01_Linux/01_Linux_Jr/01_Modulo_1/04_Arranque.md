@title: Arranque del sistema: firmware, bootloader, kernel e init
@icon: 🚀
@description: Qué ocurre desde el encendido hasta el login: firmware, GRUB, kernel, init/systemd y espacio de usuario.
@order: 4

# Arranque en Linux: desde el firmware hasta el escritorio o la consola

Bienvenido al capítulo más importante para entender por qué tu ordenador funciona... o por qué deja de hacerlo.

¿Alguna vez te has parado a pensar en lo que ocurre en esos 15 o 30 segundos que pasan desde que pulsas el botón de encendido hasta que aparece tu fondo de pantalla? Para la mayoría de la gente, es "tiempo muerto". Para un informático, es una sinfonía compleja, una carrera de relevos olímpica y un milagro de la ingeniería, todo a la vez.

En este módulo no vamos a darte una lista aburrida de pasos. Vamos a meternos dentro de los circuitos. Vamos a ver cómo una máquina inerte de metal y silicio cobra vida ("Bootstrapping").

### ¿Por qué es vital saber esto?
Imagina que eres un médico. Si un paciente llega y te dice "me duele", necesitas saber anatomía para saber dónde mirar.
*   Si el PC pita y no da vídeo, el problema es **Hardware**.
*   Si sale un texto blanco sobre fondo negro que dice "GRUB Rescue", el problema es el **Gestor de Arranque**.
*   Si sale mucho texto rápido y se congela, el problema es el **Kernel**.
*   Si te pide contraseña pero no entra al escritorio, el problema es el **Espacio de Usuario**.

Si entiendes las **4 Etapas del Arranque**, nunca más dirás "se ha roto el PC". Sabrás decir exactamente *qué* se ha roto y cómo arreglarlo.

@section: Fase 0: El Chispazo (Hardware y Electricidad)

Antes de que haya software, hay física.

Todo comienza con el dedo en el botón. Al cerrarse el circuito, la fuente de alimentación (PSU) despierta. Pero no envía electricidad a lo loco. La electricidad es peligrosa.
1.  La fuente realiza una autocomprobación interna.
2.  Si los voltajes son estables (+5V, +3.3V, +12V), envía una señal llamada **"Power Good"** a la placa base.
3.  Sin esta señal, la placa base está muerta para protegerse de sobretensiones. Por eso, si tu fuente está medio rota, el PC ni siquiera intenta encender.

Una vez que la placa recibe la señal "Power Good", despierta a la CPU (el procesador) y le da una orden: *"Despierta y busca instrucciones en la dirección de memoria FFFF0h"*.

Esa dirección apunta a un chip soldado en la placa base. El chip del **Firmware**. Aquí empieza el software.

@section: Etapa 1: El Despertador (BIOS vs UEFI)

El Firmware es el software "nativo" de tu placa base. No está en el disco duro. Está "quemado" en un chip. Su trabajo es comprobar que el hardware básico existe y funciona antes de intentar despertar al Sistema Operativo.

Aquí es donde la historia de la informática se divide en dos eras. Tienes que saber cuál usa tu PC.

### 1. La Era Antigua: BIOS (Basic Input/Output System)
La BIOS nació en los años 80 con los primeros PCs de IBM. Ha funcionado bien durante 30 años, pero es extremadamente primitiva.
*   **Es tonta:** No entiende de archivos. No sabe qué es "Windows" ni "Linux". No sabe leer una partición formateada en ext4 o NTFS.
*   **Es ciega:** Solo sabe hacer una cosa: Ir al **primer sector físico** del disco duro (llamado **MBR** o Master Boot Record), leer los primeros 512 bytes (que es poquísimo espacio) y ejecutar ciegamente lo que haya ahí.
*   **Limitaciones:** No soporta discos de más de 2TB. La interfaz es esa pantalla azul y gris horrible que se maneja solo con teclado.

### 2. La Era Moderna: UEFI (Unified Extensible Firmware Interface)
Si tu ordenador es de 2010 en adelante, tienes UEFI. Es un salto cuántico.
*   **Es inteligente:** UEFI es casi un sistema operativo en sí mismo. Entiende sistemas de archivos (concretamente **FAT32**).
*   **Es selectiva:** No lee el primer sector del disco a lo loco. Busca una partición específica en tu disco duro llamada **ESP (EFI System Partition)**.
*   **Archivos .efi:** Dentro de esa partición, busca archivos ejecutables con extensión `.efi`. Esto es mucho más seguro y robusto.
*   **Secure Boot:** UEFI puede verificar firmas digitales para asegurarse de que un virus no ha modificado tu gestor de arranque.

### El POST (Power-On Self Test)
Sea BIOS o UEFI, lo primero que hace el firmware es el POST. Es el "chequeo médico" matutino.
1.  **CPU:** ¿Estás ahí? ¿Funcionan tus registros?
2.  **RAM:** Escribe un dato en la memoria y léelo. ¿Coinciden? (Si falla, el PC suele dar pitidos largos y repetitivos).
3.  **GPU:** ¿Tengo una tarjeta gráfica para mostrar cosas? (Si falla, pitidos cortos).
4.  **Teclado:** ¿Hay algo conectado?

Si el POST pasa, el Firmware busca un dispositivo de arranque (Boot Device) según el orden que hayas configurado (USB primero, Disco Duro después, etc.).

Cuando encuentra el dispositivo, carga el **Gestor de Arranque (Bootloader)** en la memoria RAM y le pasa el testigo.
El Hardware se retira. Entra el Software.

@quiz: Estás intentando arrancar un PC y escuchas una serie de pitidos fuertes, pero la pantalla no se enciende. ¿Dónde está el problema más probable?
@option: El disco duro está roto.
@correct: Fallo de Hardware detectado por el POST (probablemente RAM o Gráfica mal asentada).
@option: Windows o Linux se han corrompido.
@option: El monitor está desenchufado.

@section: Etapa 2: El Anfitrión (GRUB)

El Firmware ha hecho su trabajo. Ahora se ejecuta el **Bootloader**.
En el mundo Windows, el bootloader es invisible (se llama *Windows Boot Manager*). En el mundo Linux, el rey indiscutible es **GRUB 2** (GRand Unified Bootloader).

GRUB es mucho más que un simple cargador. Es una herramienta de salvamento.

### ¿Por qué necesitamos un Bootloader?
El Kernel de Linux es un archivo complejo. El Firmware (especialmente la BIOS antigua) no es lo suficientemente listo para buscar ese archivo dentro de una carpeta comprimida en un disco con formato Linux avanzado. Necesitamos un intermediario.

### Las Fases de GRUB (Simplificado)
GRUB es tan grande que no cabe en el MBR (los 512 bytes del principio del disco). Así que se carga por partes, como un Transformer.
1.  **Stage 1:** Un trocito minúsculo que vive en el inicio del disco. Su única misión es saber dónde está el resto de GRUB.
2.  **Stage 1.5:** Vive en el espacio vacío entre el inicio del disco y la primera partición. Contiene los drivers del sistema de archivos (ext4, btrfs, etc.). ¡Ahora GRUB ya puede leer el disco!
3.  **Stage 2:** Carga la interfaz gráfica, el menú, el fondo de pantalla y la configuración (`/boot/grub/grub.cfg`).

### El Menú de GRUB
Aquí es donde tú interactúas. Ves una pantalla (usualmente negra o morada) con opciones:
*   `Ubuntu` (O el nombre de tu distro).
*   `Advanced options for Ubuntu` (Opciones avanzadas).
*   `Windows Boot Manager` (Si tienes Dual Boot).

**Truco de experto:** Si tu PC arranca mal tras una actualización, entra en "Advanced options". Verás versiones anteriores del Kernel. Elige una vieja. Si el PC arranca bien, significa que la actualización del Kernel estaba rota, pero tu sistema está bien. ¡GRUB te ha salvado!

### La Decisión
Una vez que pulsas Enter (o se acaba el tiempo de espera), GRUB va al disco duro, a la carpeta `/boot`, y carga dos archivos sagrados en la memoria RAM:
1.  **El Kernel (`vmlinuz`):** El núcleo del sistema operativo.
2.  **El Initramfs (`initrd`):** Un sistema de archivos temporal (explicaremos esto en detalle, es vital).

Una vez cargados en la memoria, GRUB dice: *"Mi trabajo ha terminado. Kernel, la máquina es tuya"*. Y GRUB desaparece de la memoria.

@quiz: Acabas de actualizar tu Linux y ahora no arranca (pantalla negra tras GRUB). ¿Qué es lo primero que deberías intentar?
@option: Reinstalar todo el sistema operativo.
@correct: Reiniciar, ir a "Opciones Avanzadas" en GRUB y seleccionar un Kernel anterior.
@option: Comprar un disco duro nuevo.
@option: Escribir código en la BIOS.

@section: Etapa 3: El Cerebro y la Paradoja del Huevo

Aquí es donde la cosa se pone técnicamente fascinante.
El Kernel de Linux se despierta en la RAM. Es un software increíblemente potente. Toma el control de la CPU, la memoria y los periféricos.

Pero el Kernel tiene un problema existencial grave: **La paradoja del huevo y la gallina.**

Para que el sistema funcione, el Kernel tiene que montar tu disco duro principal (la **Partición Raíz** o `/`) donde están tus programas.
PERO...
Para poder leer tu disco duro, el Kernel necesita **Drivers** (controladores) específicos (para leer el formato ext4, para entender tu controladora de disco SATA o NVMe, o para descifrar el disco si usas cifrado).
¿Y dónde están esos drivers?
¡Están dentro del disco duro que el Kernel aún no puede leer!

El Kernel no puede leer el disco sin los drivers, y no puede cargar los drivers porque están en el disco. ¿Cómo se soluciona esto?

### La Solución Mágica: Initramfs (Initial RAM Filesystem)

¿Recuerdas el segundo archivo que cargó GRUB? El `initrd.img` o `initramfs`.
Este archivo es, literalmente, una **mochila de supervivencia**.

Es un pequeño archivo comprimido (como un .zip) que contiene un **mini-sistema operativo Linux** muy básico, con los drivers justos y necesarios para arrancar.

**La Secuencia Mágica:**
1.  El Kernel, al no poder ver el disco duro real todavía, descomprime el `initramfs` en la memoria RAM.
2.  Monta este `initramfs` como si fuera un disco duro temporal.
3.  El Kernel explora este mini-disco en la RAM y ¡bingo! Ahí encuentra los drivers necesarios para leer el disco duro real.
4.  Carga los drivers. Ahora el Kernel ya tiene "gafas" para ver tu disco duro real.
5.  El Kernel monta tu disco duro real (`/`) en modo de "solo lectura" para comprobar que está sano.
6.  Si todo está bien, realiza una maniobra llamada **Pivot Root**. Cambia el sistema de archivos temporal (RAM) por el real (Disco Duro).
7.  El `initramfs` se borra de la memoria.

Si alguna vez ves un error que dice:
`Kernel Panic - not syncing: VFS: Unable to mount root fs`
Significa que la "mochila de supervivencia" (initramfs) está corrupta o le faltan los drivers para leer tu disco. El Kernel se ha quedado ciego y entra en pánico.

### El Nacimiento del Padre (PID 1)
Una vez que el disco real está montado, el Kernel busca el programa más importante de todos. El primer programa del "Espacio de Usuario".
Históricamente se llamaba `init`. Hoy en día, en la mayoría de sistemas, es un enlace a **Systemd**.

El Kernel ejecuta `/usr/lib/systemd/systemd`.
A este proceso se le asigna el **PID 1** (Process ID 1).
Todos los demás programas que ejecutes (Navegador, Spotify, Terminal) serán hijos, nietos o bisnietos de este Proceso 1. Si el Proceso 1 muere, el sistema se apaga instantáneamente (Kernel Panic).

El Kernel, satisfecho, se retira a un segundo plano para gestionar la memoria y el hardware, dejando que Systemd configure el resto.

@quiz: ¿Cuál es la función crítica del archivo `initramfs`?
@option: Contiene los fondos de pantalla de GRUB.
@correct: Provee un sistema de archivos temporal en RAM con los drivers necesarios para montar el disco duro real.
@option: Acelera la velocidad de la CPU durante el arranque.
@option: Guarda las contraseñas de los usuarios.

@section: Etapa 4: El Organizador (Systemd)

Aquí entramos en el terreno del "Espacio de Usuario". Ya no estamos en las profundidades del hardware o el Kernel. Estamos configurando el entorno para que tú, el humano, puedas trabajar.

**Systemd** es el gestor de sistemas y servicios. Su trabajo es llevar al ordenador desde "Kernel cargado" hasta "Pantalla de Login".

Antiguamente (con System V init), este proceso era lento y secuencial:
1.  Arrancar red... (esperar 5s)
2.  Arrancar sonido... (esperar 2s)
3.  Arrancar impresoras... (esperar)

Systemd es **paralelo** y agresivo. Intenta arrancar todo lo posible a la vez para que tu PC encienda en segundos.

### Los Targets (Objetivos)
Systemd no piensa en "niveles", piensa en "metas" o Targets.
El sistema tiene una meta configurada por defecto (puedes verla con `systemctl get-default`).

1.  **`basic.target`:** Lo mínimo. Montar discos, cargar drivers básicos, iniciar el socket de logs.
2.  **`multi-user.target`:** (Equivalente al antiguo Runlevel 3). Arranca la red, el SSH, y te deja en una consola de texto donde te pide login. Es el objetivo final de los **Servidores**.
3.  **`graphical.target`:** (Equivalente al antiguo Runlevel 5). Es lo mismo que el anterior, pero además arranca el **Display Manager** (GDM, SDDM, LightDM). Esto es lo que usas en tu PC de escritorio.

### El Baile de los Servicios
Systemd lee cientos de archivos de configuración (Unidades) en `/lib/systemd/system` y empieza a lanzarlos.
*   Monta los discos definidos en `/etc/fstab`.
*   Activa la partición Swap.
*   Levanta la tarjeta de red (NetworkManager).
*   Sincroniza la hora (systemd-timesyncd).
*   Inicia el Firewall.

Si algo crítico falla aquí (por ejemplo, has tocado el archivo `/etc/fstab` y has puesto mal el nombre de un disco), Systemd se detendrá y te tirará al **Emergency Mode** (una consola de texto muy básica para que arregles el error).

Si todo va bien, Systemd lanza el **Display Manager**. Aparece la pantalla de bienvenida con tu usuario. Escribes tu contraseña. El sistema descifra tu carpeta personal (si está cifrada) y carga tu Entorno de Escritorio (GNOME, KDE, etc.).

¡El viaje ha terminado!

@section: Guía de Supervivencia: Diagnosticando Fallos de Arranque

Ahora que conoces la anatomía, diagnosticar es pura lógica deductiva. Usa esta tabla cuando tu Linux no arranque:

### Escenario A: El Silencio Absoluto
*   **Síntoma:** Pulsas el botón. Ventiladores giran. Pantalla negra. Sin letras.
*   **Diagnóstico:** Fallo en **Etapa 1 (Hardware/Firmware)**.
*   **Acción:** Revisa cables, monitor, RAM. El software ni siquiera ha empezado.

### Escenario B: El Mensaje Perdido
*   **Síntoma:** Texto blanco: "No bootable device found" o "Insert Boot Media".
*   **Diagnóstico:** Fallo entre **Etapa 1 y 2**. La BIOS funciona, pero no encuentra a GRUB.
*   **Causa:** Disco duro roto, o has borrado la partición EFI, o el orden de arranque en BIOS está mal (está intentando arrancar desde un USB vacío).

### Escenario C: El Rescate de GRUB
*   **Síntoma:** Pantalla negra con texto `grub rescue>`.
*   **Diagnóstico:** Fallo crítico en **Etapa 2**. GRUB empezó a cargar, pero no encuentra sus propios archivos de configuración en el disco (la carpeta `/boot/grub` ha desaparecido o cambiado de sitio).
*   **Acción:** Necesitas un USB de instalación ("Live USB") para reinstalar GRUB.

### Escenario D: El Pánico del Kernel
*   **Síntoma:** Un montón de texto técnico en pantalla que termina con `Kernel Panic - not syncing` y dos luces del teclado parpadean.
*   **Diagnóstico:** Fallo en **Etapa 3**. El Kernel cargó, pero el `initramfs` falló, o el Kernel tiene un bug, o el hardware de memoria RAM está corrupto.
*   **Acción:** Reinicia, mantén pulsado Shift o Esc para ver el menú de GRUB, y elige una versión de Kernel anterior.

### Escenario E: El Modo de Emergencia
*   **Síntoma:** El sistema parece arrancar, salen letras de colores [OK], pero de repente se para y dice: `Welcome to emergency mode! Give root password for maintenance`.
*   **Diagnóstico:** Fallo en **Etapa 4 (Systemd)**.
*   **Causa Más Común:** Has editado el archivo `/etc/fstab` para añadir un disco duro nuevo y has cometido un error de sintaxis o el disco está desconectado. Systemd intenta montar el disco, falla, y por seguridad detiene el arranque.
*   **Acción:** Escribe la contraseña de root. Escribe `journalctl -xb` para leer los logs en rojo. Busca el error de montaje. Edita `/etc/fstab` con `nano` y corrige el error o comenta la línea problemática poniendo un `#` delante. Escribe `reboot`.

@section: Resumen Final

El arranque es una cadena de confianza donde cada eslabón levanta al siguiente:

1.  **Electricidad:** Power Good.
2.  **Firmware (UEFI/BIOS):** Chequeo de hardware -> Busca Disco.
3.  **Bootloader (GRUB):** Menú de elección -> Carga Kernel + Initramfs en RAM.
4.  **Kernel:** Usa Initramfs para tener drivers -> Monta Disco Real -> Llama al Padre.
5.  **Init (Systemd):** Lee configuración -> Lanza servicios -> Lanza Gráficos.
6.  **Tú:** Escribes tu contraseña y ves memes en internet.

Ahora, cuando veas esas letras pasar rápido en la pantalla, ya no verás "código Matrix". Verás a viejos amigos saludando: "¡Hola Kernel!", "¡Hola Systemd!", "¡Gracias por montar mis discos!".

Eres un usuario de Linux. Conoces tu máquina.

@quiz: Estás en "Emergency Mode" porque el sistema no arranca. ¿Qué comando te permite ver los logs del arranque actual para encontrar el error exacto?
@option: cat /var/log/boot.log
@correct: journalctl -xb
@option: dmesg | grep error
@option: show errors

@quiz: ¿En qué partición especial busca el sistema UEFI los archivos de arranque (`.efi`)?
@option: MBR
@option: Partición Swap
@correct: Partición EFI (ESP)
@option: En la carpeta /windows