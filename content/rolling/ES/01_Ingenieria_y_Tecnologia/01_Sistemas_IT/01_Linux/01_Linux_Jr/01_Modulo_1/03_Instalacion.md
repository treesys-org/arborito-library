@title: Instalación y almacenamiento: particiones, montaje y árbol /
@icon: 💾
@description: Particiones, puntos de montaje, fstab y un solo árbol de directorios en Linux (sin unidades tipo C:).
@order: 3

# Instalación y almacenamiento en Linux: particiones, montaje y jerarquía /

En esta lección verás cómo Linux usa **un solo árbol** bajo `/` (sin unidades tipo `C:`), qué significa **montar** un sistema de archivos, cómo planificar **particiones** o volúmenes y cómo encaja **fstab** en el arranque y el día a día.

> **ADVERTENCIA:** Este es un capítulo largo. Muy largo. Pero es necesario. Si vienes de Windows, tu cerebro está cableado para pensar en "Unidades" (C:, D:). Linux funciona de una manera fundamentalmente diferente. Si no entiendes esto, te sentirás perdido para siempre. Si lo entiendes, verás que la forma de Windows es la extraña.

@section: 1. El Trauma de la Unidad C:

Empecemos con lo que más asusta a los recién llegados. Conectas tu pendrive USB. Abres el explorador de archivos. Buscas la unidad `E:` o `F:`.
**No existe.**
Buscas `Mi PC`.
**No existe.**

En Windows, el almacenamiento se basa en el concepto de **Islas**.
*   Tu disco duro principal es la Isla `C:`.
*   Tu segundo disco es la Isla `D:`.
*   Tu USB es la Isla `E:`.
Estas islas no se tocan. Son mundos separados.

En Linux (y en UNIX, y en macOS), el almacenamiento se basa en el concepto del **Árbol Único**.
*   Existe una sola raíz: **`/`** (La Raíz o Root).
*   **Todo** cuelga de ahí. Absolutamente todo.
*   Si conectas un disco duro nuevo, no creas una isla nueva. Lo que haces es "pegar" o "injertar" ese disco en una rama del árbol existente.

### La Analogía de la Mansión
Imagina que tu ordenador es una **Mansión Gigante**.
*   **Windows:** Cada vez que compras una habitación nueva (un disco duro), construyes una casita separada en el jardín y le pones una letra (C, D, E). Para ir de una a otra, tienes que salir a la calle.
*   **Linux:** La mansión tiene una entrada principal (`/`). Cuando compras una habitación nueva (un disco duro), abres una puerta en el pasillo y pegas la habitación ahí. Desde dentro, parece que la mansión simplemente ha crecido. No hay costuras. Todo está bajo el mismo techo.

Esto se llama **El Sistema de Archivos Unificado**.

@quiz: En la filosofía de Linux, ¿cómo se representa un segundo disco duro conectado al sistema?
@option: Como una unidad separada con una letra (ej. D:).
@correct: Como un directorio más dentro del árbol principal (ej. /mnt/disco2).
@option: No se puede conectar un segundo disco.

@section: 2. El Mapa de la Mansión (FHS)

Para que no te pierdas en esta mansión infinita, existe un plano arquitectónico estandarizado llamado **FHS (Filesystem Hierarchy Standard)**. No es una ley física: es un **estándar** que describen el *Linux Foundation* y documentos como el FHS 3.0 (2015). Las distribuciones lo siguen casi al pie de la letra porque así los administradores, los paquetes y las certificaciones (por ejemplo **LPIC-1**, tema 104.1) hablan el mismo idioma.

**¿Por qué te importa?** Porque cuando alguien dice “mira en `/etc`” o “el log está en `/var/log`”, no es capricho: es el mapa común. Saber el FHS te ahorra horas de búsqueda y te permite leer documentación de Debian, Fedora o RHEL sin reescribir mentalmente las rutas cada vez.

### Historia mínima y el “merge” de `/usr`

En sistemas antiguos, `/bin` y `/usr/bin` tenían roles más separados: lo mínimo para arrancar en `/`, y casi todo lo demás bajo `/usr` (a veces en otro disco). En **Linux moderno**, muchas distribuciones aplicaron la **fusión de jerarquía** (*usr merge*): `/bin`, `/sbin` y `/lib` son **enlaces simbólicos** hacia `/usr/bin`, `/usr/sbin` y `/usr/lib`. Así el sistema arranca desde una única partición grande y coherente. Si ejecutas `ls -l /bin` y ves `bin -> usr/bin`, estás viendo ese diseño. No te asustes: el FHS sigue siendo válido; solo cambia **dónde** vive físicamente el contenido.

### `/` (La raíz)

El inicio de todo. Solo el superusuario debería escribir aquí con criterio; un usuario normal crea y edita sobre todo en su `/home`. Las rutas que empiezan por `/` son **absolutas** (`/etc/hosts`); las que no, son **relativas** al directorio actual.

**No confundas:** la raíz del árbol es `/`. La carpeta personal del usuario root es `/root`. Son tres conceptos distintos: raíz del sistema, cuenta privilegiada, y directorio home de esa cuenta.

### `/boot` (Arranque)

Aquí viven el **kernel** (`vmlinuz-*`), los **initramfs** (imagen inicial con módulos mínimos) y, en sistemas UEFI, a menudo enlazas con la partición EFI montada en `/boot/efi`. Si borras o corrompes `/boot`, el sistema puede no arrancar aunque `/` esté intacto. Por eso las actualizaciones de kernel instalan nuevos ficheros aquí y actualizan el cargador (GRUB).

### `/bin` y `/usr/bin` (Programas de usuario)

“Binario” = **ejecutable**. Cuando escribes `ls`, `cp`, `cat` o `mkdir`, el shell busca el programa según tu variable `PATH` (normalmente incluye `/usr/bin` y `/bin`).

*   **`/bin`:** en teoría, lo imprescindible incluso en rescate; en distros fusionadas apunta a `/usr/bin`.
*   **`/usr/bin`:** la mayor parte de comandos de usuario y muchas aplicaciones sin interfaz gráfica.

### `/sbin` y `/usr/sbin` (Administración del sistema)

Herramientas para tareas de administración: `fdisk`, `ip`, `iptables`/`nft`, `systemctl` en muchos casos, etc. No implica que solo root pueda ejecutarlas (algunos tienen permisos especiales), pero **sí** que no son el cajón de útiles del usuario casual.

### `/etc` (Configuración)

**La habitación más visitada del sysadmin.** Casi toda la configuración es **texto plano** (a veces con formato `.yaml` o `.json`, pero legible).

*   Ejemplos: `/etc/passwd`, `/etc/group`, `/etc/hosts`, `/etc/fstab`, `/etc/ssh/sshd_config`.
*   Muchos servicios usan **directorios `*.d`**: fragmentos que se incluyen en orden (`conf.d`, `sudoers.d`). Así los paquetes añaden un archivo sin pisar el tuyo.

**Regla de oro:** copia antes de editar: `sudo cp archivo archivo.bak`. Para cambios controlados, usa el editor que prefieras y documenta qué tocaste.

### `/home` (Directorios personales)

Linux es **multiusuario**. Cada usuario tiene `/home/usuario` (excepto convenciones especiales). Ahí viven documentos, proyectos y **dotfiles** (`.bashrc`, `.config`, `.ssh`). Los permisos predeterminados suelen ser `700` o similares para que otros usuarios no lean tu privacidad.

### `/root` (Home del superusuario)

No está bajo `/home` para que siga siendo accesible si `/home` está en otra partición rota o no montada durante el arranque en modo rescate.

### `/var` (Datos variables)

Datos que **cambian** en tiempo de ejecución: logs, colas de correo, cachés de paquetes, bases bajo `/var/lib`, sitios web bajo `/var/www` en muchas instalaciones, **spool** de impresión, etc. Llenar el disco con logs o bases aquí es un fallo operativo típico: por eso se monitoriza `/var` (y a veces se monta en partición aparte).

### `/tmp` y `/var/tmp` (Temporales)

*   **`/tmp`:** mundo escribible con sticky bit; adecuado para ficheros cortos. A menudo es **tmpfs** (en RAM) y se limpia al reiniciar.
*   **`/var/tmp`:** temporales que deben **sobrevivir** a reinicios (según política del sistema).

Nunca guardes secretos ni datos críticos en temporales: cualquier usuario local puede listar y a veces interferir según permisos.

### `/dev` (Dispositivos)

**“Todo es un archivo.”** Discos (`/dev/sda`, particiones `sda1`), terminales (`/dev/tty*`), aleatorio (`/dev/urandom`), null (`/dev/null`). Los números en `ls -l` (major/minor) identifican el driver en el kernel.

### `/proc` y `/sys` (Vistas al kernel)

No son discos reales: son **sistemas de archivos virtuales**. `/proc` expone procesos (`/proc/<pid>/`), `cpuinfo`, `meminfo`, parámetros tunables bajo `sysctl`. `/sys` describe dispositivos y el árbol de hardware de forma jerárquica. Leerlos es la forma “Unix pura” de inspeccionar el sistema; herramientas como `lscpu` o `free` en realidad leen estos datos.

### `/run` (Estado de ejecución)

Sustituyó en la práctica a gran parte de lo que antes vivía en `/var/run`. Contiene **PID files**, sockets de demonios y estado **efímero** que no debe persistir entre arranques. En muchos sistemas `/var/run` es enlace a `/run`.

### `/srv` (Datos de servicios)

Convención para datos **entregados por un servicio** (FTP, repos internos, etc.). No siempre se usa: muchas distros ponen la web en `/var/www`; en diseños nuevos a veces verás `/srv/www`. Lo importante es **documentar** dónde está el dato en *tu* entorno.

### `/mnt` y `/media` (Puntos de montaje)

*   **`/media`:** montajes automáticos (USB, discos externos) por el escritorio o udisks.
*   **`/mnt`:** suelen crearse subdirectorios bajo él para montajes **manuales** de administrador (`/mnt/backup`).

### `/lib`, `/lib64` y compañía (Bibliotecas)

Bibliotecas compartidas (equivalente a `.dll`) y módulos que necesitan los binarios. En sistemas de 64 bits verás `lib64` o rutas bajo `/usr/lib/x86_64-linux-gnu` (Debian/Ubuntu). Los **módulos del kernel** suelen vivir bajo `/lib/modules/<versión-kernel>/`.

### `/usr` (Jerarquía secundaria de solo lectura… en teoría)

Contiene la mayor parte de programas y datos de solo lectura del sistema operativo “normal”: `/usr/share` (documentación, iconos, zonas horarias), `/usr/include` (cabeceras de desarrollo), `/usr/src` (fuentes del kernel o paquetes). En instalación típica, **casi todo lo que no es configuración mutable** está aquí o en `/var`.

### `/usr/local` (Software instalado en el sitio)

Software compilado o instalado **manualmente** en esa máquina (no por el gestor de paquetes de la distro) suele ir a `/usr/local/bin`, `/usr/local/lib`, etc. Así no se mezcla con paquetes del distribuidor. En servidores con software propio, este directorio es sagrado.

### `/opt` (Paquetes de aplicación aislados)

Terceros empaquetan ahí su árbol completo (`/opt/google/chrome` estilo). Es “opcional” en el sentido de que la distro base puede vivir sin ello.

### `lost+found` (Basura de recuperación)

En la raíz de cada sistema de archivos **ext** aparece este directorio. `fsck` deposita allí fragmentos recuperados tras una reparación. Ojalá nunca tengas que mirarlo.

### Tabla mental para el examen (resumen LPIC-104.1)

| Ruta | Recuerda en una frase |
| :--- | :--- |
| `/etc` | Configuración del sistema |
| `/var` | Datos que crecen y cambian (logs, datos de apps) |
| `/home` | Usuarios |
| `/tmp`, `/var/tmp` | Temporales |
| `/usr` | Programas y datos de solo lectura “estándar” |
| `/usr/local` | Instalaciones locales del administrador |
| `/opt` | Grandes aplicaciones de terceros |
| `/boot` | Kernel e initramfs |
| `/dev` | Nodos de dispositivo |
| `/proc`, `/sys` | Interfaz al kernel |

### Laboratorio rápido (15 minutos)

1.  `ls -l /` — identifica symlinks (`bin -> usr/bin`, etc.).
2.  `man hier` — página de manual que describe la jerarquía (nombre puede variar; si no existe, `man file-hierarchy` en systemd).
3.  `readlink -f /bin/sh` — qué shell predeterminado usa realmente el sistema.
4.  `cat /proc/cpuinfo | head` y `cat /proc/meminfo | head` — lectura directa del kernel.
5.  `df -h` y `lsblk` — relaciona montajes con el mapa mental.

@quiz: ¿En qué directorio buscarías los archivos de configuración del sistema (como la configuración de red o de usuarios)?
@option: /bin
@option: /home
@correct: /etc
@option: /var

@section: 3. El Concepto de "Montar" (Mounting)

Esto es lo que más confunde a los usuarios de Windows. Vamos a explicarlo despacio.

Tienes un pendrive USB lleno de fotos.
1.  Lo enchufas al puerto USB.
2.  El Kernel (el núcleo) lo detecta. Dice: *"He encontrado un dispositivo de almacenamiento masivo. Lo llamaré `/dev/sdb`"*.
3.  Pero `/dev/sdb` es solo un archivo de dispositivo crudo. No puedes entrar en él. No puedes ver las fotos.

Para usarlo, tienes que **Montarlo**.
Montar significa: *"Toma el sistema de archivos que hay dentro de este dispositivo y hazlo accesible en esta carpeta de mi árbol"*.

**El Proceso Manual (lo que hace el sistema por ti):**
1.  Creas una carpeta vacía donde quieras que aparezcan las fotos:
    `mkdir /mnt/mis_fotos`
2.  Le das la orden de montaje:
    `mount /dev/sdb1 /mnt/mis_fotos`
    *(Nota: sdb1 es la primera partición del disco sdb)*.
3.  ¡Magia! Ahora, si entras en `/mnt/mis_fotos`, verás el contenido del USB. Esa carpeta es ahora una ventana al dispositivo.

**Desmontar:**
Antes de sacar el USB, tienes que decirle al sistema que deje de usarlo para que termine de escribir datos pendientes.
`umount /mnt/mis_fotos`
(Fíjate que es `umount`, sin la 'n' después de la 'u').

@quiz: ¿Qué comando se utiliza para desconectar de forma segura un dispositivo montado antes de retirarlo físicamente?
@option: disconnect
@option: unmount
@correct: umount
@option: eject

@section: 4. Sistemas de Archivos: El Idioma del Disco

Un disco duro, físicamente, es como un cuaderno en blanco gigante. Para escribir en él, necesitas dibujar líneas, márgenes y números de página. Necesitas unas reglas sobre cómo guardar la información. Esas reglas son el **Sistema de Archivos (Filesystem)**.

Windows y Linux hablan idiomas diferentes.

### Los Idiomas de Windows
*   **FAT32:** El idioma universal. Viejo, simple, compatible con todo (Windows, Mac, Linux, consolas, TVs).
    *   *Desventaja:* No admite archivos de más de 4GB. Olvida guardar películas en 4K.
*   **NTFS:** El estándar moderno de Windows.
    *   *Linux:* Linux puede leerlo y escribirlo perfectamente hoy en día, pero no puede instalarse *sobre* él. Linux no puede "vivir" en una partición NTFS porque NTFS no entiende los permisos de usuario de Linux.

### Los Idiomas de Linux
Para instalar Linux, necesitas formatear el disco en un idioma que Linux entienda nativamente.

#### 1. Ext4 (Fourth Extended Filesystem)
El estándar de oro. El "Toyota Corolla" de los discos.
*   **Pros:** Sólido como una roca. Muy probado. Es casi imposible perder datos por culpa del sistema de archivos.
*   **Journaling (Diario):** Esta es su característica estrella. Antes de escribir un archivo en el disco, Ext4 escribe una nota en un "diario" especial: *"Voy a escribir el archivo foto.jpg en el sector 500"*.
    *   Si se va la luz en medio de la escritura, al reiniciar, el sistema lee el diario, ve que la operación quedó a medias, y la arregla (o la termina o la deshace). Sin esto, tu disco quedaría corrupto.
*   **Uso:** Es el defecto en Ubuntu, Mint, Debian. Úsalo si eres nuevo.

#### 2. Btrfs (B-Tree Filesystem)
El sistema "Next-Gen". Es mucho más avanzado.
*   **Copy-on-Write (CoW):** Nunca sobrescribe datos. Si modificas un archivo, escribe los cambios en un espacio libre y luego actualiza el puntero. Esto evita corrupción.
*   **Snapshots (Instantáneas):** Puedes tomar una "foto" de todo tu disco en 1 segundo. Ocupa casi 0 espacio.
    *   *Caso de uso:* Antes de actualizar el sistema, tomas un snapshot. Si la actualización rompe tu Linux, reinicias, seleccionas el snapshot de hace 5 minutos, y el sistema vuelve *exactamente* a como estaba. Es como magia.
*   **Uso:** Defecto en Fedora y openSUSE.

#### 3. XFS
El gigante empresarial.
*   **Pros:** Increíblemente rápido manejando archivos gigantescos y bases de datos masivas. Se usa en servidores de la NASA y Facebook.
*   **Contras:** Es más complejo de reducir de tamaño.
*   **Uso:** Servidores Red Hat Enterprise Linux.

### ¿Fragmentación?
En Windows, tenías que "Desfragmentar el disco" porque los archivos se partían en trozos dispersos.
Los sistemas de Linux (Ext4, XFS) son mucho más inteligentes. Cuando escriben un archivo, buscan un espacio lo suficientemente grande para que quepa entero, o dejan espacio extra alrededor para que crezca.
**En Linux, NO necesitas desfragmentar.** De hecho, hacerlo en un SSD es perjudicial.

@section: 5. Particionado: Dividiendo el Pastel

Cuando instalas Linux, tienes que decidir cómo dividir tu disco duro (Pizza). Cada trozo es una **Partición**.

### MBR vs. GPT
Antes de particionar, el disco necesita una "Tabla de Particiones" (el índice del libro).
*   **MBR (Master Boot Record):** El sistema antiguo (años 80). Solo permite 4 particiones primarias y discos de hasta 2TB. Está obsoleto.
*   **GPT (GUID Partition Table):** El estándar moderno. Permite particiones ilimitadas y discos de tamaño Zettabyte. Es obligatorio para arrancar en modo UEFI.
**Consejo:** Usa siempre GPT a menos que tu ordenador sea del año 2008.

### Esquema de Particionado Recomendado (2025)
Si vas a instalar Linux en un PC moderno (UEFI), este es el esquema profesional y seguro:

#### 1. Partición EFI (`/boot/efi`)
*   **Tamaño:** 300 MB - 512 MB.
*   **Formato:** FAT32 (Obligatorio).
*   **Función:** Es la única partición que la placa base (BIOS/UEFI) sabe leer al encenderse. Aquí vive el **Bootloader** (GRUB), el programa que te deja elegir si arrancar Windows o Linux.

#### 2. Partición Raíz (`/`)
*   **Tamaño:** Mínimo 30 GB. Recomendado 50 GB - 100 GB.
*   **Formato:** Ext4 (o Btrfs).
*   **Función:** Aquí se instala el Sistema Operativo y todos los programas. Es el equivalente a `C:\Windows` + `C:\Archivos de Programa`.

#### 3. Partición Home (`/home`) - ¡MUY RECOMENDADO!
*   **Tamaño:** Todo el espacio restante.
*   **Formato:** Ext4.
*   **Función:** Aquí van tus datos personales (Documentos, Fotos, Vídeos, Configuración de usuario).
*   **¿Por qué separarla?** Esta es la gran ventaja de Linux. Si mañana sale una versión nueva de Ubuntu, o quieres cambiarte a Fedora, puedes **formatear la partición Raíz (`/`)** para instalar el sistema nuevo, pero **dejar la partición `/home` intacta**.
    *   Al terminar de instalar, ¡todos tus archivos y tus configuraciones seguirán ahí! No tienes que copiar datos a un disco externo. Es la inmortalidad de los datos.

#### 4. Swap (Área de Intercambio)
La Swap es la "Memoria Virtual". Si tu RAM se llena (abres 50 pestañas de Chrome), Linux mueve las cosas más viejas de la RAM a la Swap para que el PC no se congele.
*   **Antiguamente:** Se hacía una partición separada.
*   **Modernamente (Swapfile):** Se usa un **archivo** gigante dentro de la partición raíz (`/swapfile`). Es mejor porque puedes cambiarle el tamaño fácilmente.
    *   Ubuntu y Mint usan Swapfile por defecto. No necesitas crear partición Swap.

### Resumen del Plan de Instalación:
1.  **EFI:** 500 MB (FAT32).
2.  **Raíz (/):** 100 GB (Ext4).
3.  **Home (/home):** Resto del disco (Ext4).

@quiz: ¿Cuál es la principal ventaja de tener `/home` en una partición separada?
@option: Hace que el sistema arranque más rápido.
@correct: Permite reinstalar o cambiar el sistema operativo formateando `/` sin perder tus archivos personales ni configuraciones.
@option: Ahorra espacio en el disco duro.

@section: 6. Comandos para Explorar Discos

Ahora que sabes la teoría, aquí tienes los comandos para ver esto en tu terminal.

### `lsblk` (List Block Devices)
Muestra todos los discos y particiones en forma de árbol. Es el comando más útil para ver qué tienes conectado.
```bash
$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 238.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
└─sda3   8:3    0 188.0G  0 part /home
```
Aquí ves claramente:
*   `sda`: El disco físico.
*   `sda1`: Partición EFI.
*   `sda2`: Partición Raíz (Sistema).
*   `sda3`: Partición Home (Datos).

### `df -h` (Disk Free)
Muestra el espacio libre y usado de los discos montados. La `-h` es para "Human Readable" (GB en lugar de bytes).
```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        50G   15G   32G  30% /
/dev/sda3       188G  100G   88G  53% /home
```

### `du -sh` (Disk Usage)
Calcula el tamaño de una carpeta.
*   `-s`: Summary (Resumen, no me listes cada archivo).
*   `-h`: Human Readable.
```bash
# ¿Cuánto ocupa mi carpeta de Documentos?
$ du -sh ~/Documentos
1.5G    /home/juan/Documentos
```

@section: 7. Permisos Básicos en Discos

Una última cosa crítica. A diferencia de Windows (versiones domésticas), Linux es estricto con quién puede ver qué disco.

Si montas un disco como `root` (el sistema lo hace al arrancar), a veces un usuario normal no puede escribir en él a menos que se le den permisos.
El archivo `/etc/fstab` (Filesystem Table) es el "mapa maestro" que le dice a Linux qué discos montar al arrancar y con qué permisos.
**¡NO TOQUES `/etc/fstab` si no sabes lo que haces!** Un error aquí puede hacer que el sistema no arranque.

@quiz: Quieres ver una lista visual rápida de tus discos, particiones y dónde están montadas. ¿Qué comando usas?
@option: df -h
@correct: lsblk
@option: du -sh
@option: fdisk

@section: Conclusión

Acabas de aprender los cimientos sobre los que se construye todo Linux.
1.  No hay C:, hay un Árbol (`/`).
2.  Tus archivos están en `/home`.
3.  La configuración está en `/etc`.
4.  Los dispositivos externos se "montan".
5.  Ext4 es tu amigo confiable.
6.  Separar `/home` es de sabios.

¡Bienvenido a la arquitectura del sistema operativo más potente del mundo!