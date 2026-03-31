@title: ZFS y Btrfs: snapshots, CoW y gestión integrada
@icon: 📚
@description: Sistemas de archivos copy-on-write: subvolúmenes, snapshots, comprobación de datos y compresión; cuándo usar ZFS o Btrfs.
@order: 4

# ZFS y Btrfs: sistemas de archivos avanzados en Linux

Bienvenido a la verdadera revolución del almacenamiento de datos.

Si vienes del mundo tradicional (Windows con NTFS, o Linux con ext4 sobre LVM), estás acostumbrado a pensar en el almacenamiento como una pila de capas rígidas:
1.  Disco Físico.
2.  Partición.
3.  Sistema de Archivos.
4.  Datos.

Este modelo ha funcionado durante 30 años, pero es **frágil**. Es como tener una biblioteca donde el bibliotecario sabe dónde están los libros, pero nunca abre las páginas para ver si se han borrado o si las polillas se las han comido. Si un bit se corrompe en tu disco duro (algo que pasa más a menudo de lo que crees), ext4 te entregará el archivo corrupto sin rechistar. Tu foto se verá gris por la mitad, o tu base de datos dará un error extraño.

Los sistemas de archivos de nueva generación, **ZFS** y **Btrfs**, cambian las reglas del juego. No son solo sistemas de archivos; son **Sistemas de Gestión de Almacenamiento Completos**.

En esta guía masiva, vamos a diseccionar estas tecnologías. No solo aprenderás los comandos; entenderás la magia matemática que protege tus datos contra el caos del universo.

@section: 1. La Filosofía Next-Gen: ¿Por qué cambiar?

Antes de teclear un solo comando, necesitas entender los tres pilares que hacen que ZFS y Btrfs sean superiores a cualquier cosa anterior.

### Pilar A: Copy-on-Write (CoW) - La Inmortalidad de los Datos

En un sistema clásico (Overwrite o Sobrescritura), como ext4 o NTFS:
1.  Abres un archivo de Word `documento.docx`.
2.  Cambias una frase.
3.  Guardas.
4.  El sistema va al lugar físico del disco donde estaba el archivo viejo y **sobrescribe** los datos antiguos con los nuevos.

**El Peligro:** Si se va la luz en el milisegundo exacto en que la cabecera magnética está escribiendo, te quedas con un archivo que es mitad viejo y mitad nuevo. El resultado es corrupción total. El archivo es ilegible.

En un sistema **CoW (ZFS/Btrfs)**:
1.  Abres el archivo.
2.  Cambias una frase.
3.  Guardas.
4.  El sistema busca un espacio libre y virgen en el disco.
5.  Escribe los datos nuevos en ese espacio libre.
6.  Solo cuando la escritura ha terminado y se ha verificado que es correcta, el sistema actualiza el "puntero" (el índice) para que apunte a la nueva ubicación.
7.  El espacio antiguo se marca como libre (o se guarda si tienes un Snapshot).

**El Resultado:** Si se va la luz, la escritura nueva falla, pero el puntero nunca se movió. **Tus datos viejos siguen intactos.** El sistema es transaccional y atómico. Nunca pierdes el estado anterior.

### Pilar B: Checksums y Autocuración (Self-Healing)

¿Cómo sabes si un archivo ha cambiado sin que tú lo toques? Los rayos cósmicos, la degradación magnética o un firmware de disco defectuoso pueden cambiar un 0 por un 1. Esto se llama **Bit Rot** (Podredumbre de Bits).

*   **ext4:** No tiene ni idea. Si le pides el archivo, te da los bits que hay en el disco. Si están mal, tu foto sale con rayas verdes.
*   **ZFS/Btrfs:** Calculan una suma de verificación (Checksum o Hash) de **CADA BLOQUE DE DATOS** que escriben. Guardan el dato y guardan su "huella dactilar" matemática en un lugar separado (en el puntero padre).

Cuando lees un archivo en ZFS:
1.  Lee el dato del disco.
2.  Calcula su huella dactilar en tiempo real.
3.  La compara con la huella guardada.
4.  **Si no coinciden:** SABE que el dato está corrupto.
5.  **Si tienes redundancia (RAID):** Automáticamente lee la copia del otro disco, verifica que su huella sea correcta, te entrega el dato bueno y **repara el dato malo en el disco original**. Todo en microsegundos, sin que te enteres.

### Pilar C: Gestión de Volúmenes Integrada

Olvida el particionado (`fdisk`, `parted`). Olvida decidir "daré 20GB a root y 50GB a home". Eso es del siglo XX.
En ZFS y Btrfs, creas una "Piscina" (Pool) con todos tus discos. Luego creas "Datasets" o "Subvolúmenes".
Todos comparten el espacio libre. No tienes que redimensionar nada. Es como tener una cuenta bancaria conjunta para todos tus archivos.

@quiz: Estás editando un documento crítico en un servidor con sistema de archivos ext4 y ocurre un corte de energía justo en el momento de guardar. Al reiniciar, el archivo está corrupto e ilegible. ¿Qué característica de ZFS/Btrfs habría prevenido esto?
@option: Deduplicación.
@option: Compresión LZ4.
@correct: Copy-on-Write (CoW).
@option: RAID 0.

@section: 2. ZFS: El Emperador del Almacenamiento

ZFS (Zettabyte File System) nació en Sun Microsystems. Es, posiblemente, la pieza de software de almacenamiento más avanzada jamás creada. Se usa en bancos, gobiernos y servidores de la NASA.

**Nota sobre la Licencia:** ZFS tiene una licencia CDDL, que es incompatible con la GPL de Linux. Por eso ZFS no viene "dentro" del Kernel, sino como un módulo externo (OpenZFS). Sin embargo, en Ubuntu y Debian, la instalación es trivial.

### Instalación en Linux
En Ubuntu/Debian, ZFS es un ciudadano de primera clase.

```bash
$ sudo apt update
$ sudo apt install zfsutils-linux
```

Vamos a verificar que el módulo del kernel se ha cargado:
```bash
$ sudo modprobe zfs
$ lsmod | grep zfs
```
Si ves salida, estás listo para construir el futuro.

### Conceptos de Arquitectura ZFS

Necesitas aprender un vocabulario nuevo. ZFS no usa los términos de siempre.

1.  **VDEV (Virtual Device):** Es la unidad básica de redundancia. Puede ser un disco solo, un espejo (mirror) de dos discos, o un grupo RAID-Z.
    *   *Analogía:* Es un "ladrillo" de almacenamiento.
2.  **zpool (Pool):** Es la agrupación de uno o más VDEVs. Es la "piscina" de espacio total.
3.  **Dataset:** Es lo que parece una partición o carpeta montada. Tiene propiedades (compresión, cuotas) pero comparte el espacio del pool.
4.  **Zvol:** Es un dispositivo de bloque virtual. Se usa si necesitas formatear con ext4 encima de ZFS (por ejemplo, para una máquina virtual que necesita un "disco duro" virtual).

@section: 3. Laboratorio ZFS: Construyendo el Tanque

Vamos a simular que tenemos 4 discos duros para jugar: `/dev/sdb`, `/dev/sdc`, `/dev/sdd`, `/dev/sde`.
*(Si estás en una VM, añade 4 discos virtuales de 10GB).*

### Escenario A: El Espejo Simple (Mirror)
Queremos máxima velocidad de lectura y máxima seguridad para una base de datos. Usaremos un espejo (equivalente a RAID 1).

**Comando de creación:**
```bash
# zpool create [NOMBRE] [TIPO] [DISCOS]
$ sudo zpool create -f tanque espejo mirror /dev/sdb /dev/sdc
```
*(Usamos `-f` para forzar si los discos tenían datos antiguos).*

Verifica el estado:
```bash
$ sudo zpool status
  pool: tanque
 state: ONLINE
scan: none requested
config:

        NAME        STATE     READ WRITE CKSUM
        tanque      ONLINE       0     0     0
          mirror-0  ONLINE       0     0     0
            sdb     ONLINE       0     0     0
            sdc     ONLINE       0     0     0

errors: No known data errors
```
¡Felicidades! Has creado tu primer pool. Se ha montado automáticamente en `/tanque`.
Haz `df -h` y verás el espacio disponible.

### Escenario B: RAID-Z (La versión ZFS de RAID 5)
Ahora queremos maximizar el espacio para un servidor de archivos, tolerando el fallo de 1 disco.
Vamos a destruir el pool anterior y crear uno nuevo con RAID-Z1 usando 3 discos.

```bash
$ sudo zpool destroy tanque
$ sudo zpool create -f tanque raidz1 /dev/sdb /dev/sdc /dev/sdd
```

Si haces `zpool status`, verás la estructura `raidz1`.
La capacidad será la suma de 2 discos (el tercero es paridad).

### Escenario C: Añadir capacidad (Stripe de VDEVs)
Imagina que tu `tanque` (formado por 3 discos en RAID-Z1) se llena.
Compras 3 discos más. ¿Cómo expandes?
En ZFS, añades otro VDEV al pool.

```bash
# Añadimos otro grupo raidz1 de 3 discos (imaginarios aquí)
# sudo zpool add tanque raidz1 /dev/sdf /dev/sdg /dev/sdh
```
Ahora tu pool es una "franja" (Stripe) de dos grupos RAID-Z1. Los datos se reparten entre ambos grupos. La velocidad se duplica.

**ADVERTENCIA CRÍTICA:**
En ZFS, **normalmente NO puedes quitar VDEVs de un pool**. Si añades un disco por error, se queda ahí para siempre (hasta versiones muy recientes que permiten algunas excepciones).
Si añades un disco "single" a un pool que era "mirror", has comprometido la seguridad de todo el pool. Si ese disco single falla, pierdes TODO el pool.
**Regla:** Siempre añade VDEVs del mismo nivel de redundancia que los existentes.

@section: 4. Datasets: Gestión Fina

Tenerlo todo en `/tanque` es sucio. Queremos organizar.
No creamos carpetas con `mkdir`. Creamos **Datasets**.

```bash
$ sudo zfs create tanque/proyectos
$ sudo zfs create tanque/backups
$ sudo zfs create tanque/isos
```

Ahora si haces `zfs list`:
```text
NAME              USED  AVAIL     REFER  MOUNTPOINT
tanque            100K  58.9G       24K  /tanque
tanque/backups     24K  58.9G       24K  /tanque/backups
tanque/isos        24K  58.9G       24K  /tanque/isos
tanque/proyectos   24K  58.9G       24K  /tanque/proyectos
```
Todos comparten los 58.9G libres.

### Propiedades Mágicas
Lo genial de los Datasets es que puedes configurarlos individualmente.

**1. Compresión (LZ4):**
Activa esto SIEMPRE. LZ4 es un algoritmo de compresión extremadamente rápido. Tan rápido que a menudo **acelera el sistema**.
¿Por qué? Porque la CPU comprime los datos más rápido de lo que el disco duro puede escribirlos. Si un archivo de 100MB se comprime a 50MB, el disco escribe la mitad de datos. ¡Doble velocidad de escritura!

```bash
$ sudo zfs set compression=lz4 tanque/proyectos
```

**2. Cuotas:**
El departamento de Marketing está llenando el servidor de vídeos 4K. Ponles un límite.
```bash
$ sudo zfs set quota=10G tanque/isos
```
Ahora `/tanque/isos` solo puede crecer hasta 10GB, aunque el pool tenga 100TB libres.

**3. Puntos de Montaje:**
No te gusta que estén en `/tanque/...`. Quieres que backups esté en `/mnt/seguridad`.
```bash
$ sudo zfs set mountpoint=/mnt/seguridad tanque/backups
```
ZFS desmonta y remonta automáticamente en el nuevo sitio. Sin editar `/etc/fstab`.

@quiz: ¿Por qué activar la compresión LZ4 en ZFS puede aumentar la velocidad de escritura en lugar de disminuirla?
@option: Porque LZ4 elimina los archivos innecesarios automáticamente.
@option: Porque ZFS usa la GPU para comprimir.
@correct: Porque la CPU comprime los datos mucho más rápido de lo que el disco puede escribir físicamente, reduciendo la cantidad de datos que viajan al disco.
@option: Solo aumenta la velocidad en discos SSD, no en mecánicos.

@section: 5. Snapshots y Clones: Viajes en el Tiempo

Aquí es donde ZFS brilla. Un Snapshot es una foto instantánea del sistema de archivos. Gracias al CoW, **hacer un snapshot cuesta 0 espacio y 0 tiempo** en el momento de hacerlo.

### Creando una Foto
Vamos a crear un archivo en proyectos.
```bash
$ echo "Datos importantes v1" > /tanque/proyectos/tesis.txt
```

Ahora, tomamos un snapshot:
```bash
$ sudo zfs snapshot tanque/proyectos@version1
```
Listo. Instantáneo.

Ahora, vamos a "destruir" el trabajo.
```bash
$ echo "Datos corruptos y erróneos" > /tanque/proyectos/tesis.txt
```

Si miramos el archivo, está mal. ¡Pánico!
Pero tenemos el snapshot.

### Accediendo al Pasado (Directorio Oculto)
ZFS tiene una característica increíble. Dentro de cada dataset, hay una carpeta oculta `.zfs/snapshot`.
```bash
$ ls /tanque/proyectos/.zfs/snapshot/version1/
tesis.txt
```
Ahí está tu archivo viejo. Puedes copiarlo con `cp` para recuperarlo. Es como tener un túnel del tiempo accesible por el usuario.

### Rollback (Vuelta Atrás Total)
Si el desastre es total (has borrado mil archivos), puedes revertir el dataset entero al estado de la foto.
```bash
$ sudo zfs rollback tanque/proyectos@version1
```
En un parpadeo, el sistema de archivos ha vuelto al pasado. Cualquier cambio posterior al snapshot se pierde para siempre.

### Clones
Un clon es un snapshot escribible.
Imagina que tienes una base de datos de 1TB. Quieres hacer una copia para que el equipo de desarrollo haga pruebas.
Copiar 1TB tarda horas y gasta 1TB.
En ZFS, haces un snapshot y lo clonas.
```bash
$ sudo zfs snapshot tanque/db@gold_image
$ sudo zfs clone tanque/db@gold_image tanque/db_dev
```
¡Boom! Tienes una copia exacta instantánea montada en `/tanque/db_dev`. Ocupa 0 bytes. Solo ocupará espacio cuando los desarrolladores empiecen a cambiar datos en ella (guardando solo las diferencias).

@section: 6. Mantenimiento: Scrubbing y Resilvering

Un pool ZFS necesita cuidados.

### El Scrub (Fregar)
Es vital programar un "Scrub" periódico (una vez al mes).
El Scrub lee **todos** los datos del disco, recalcula sus checksums y verifica que coinciden con los metadatos.
Si encuentra un bit corrupto (Bit Rot) y tiene redundancia, lo repara silenciosamente.

```bash
$ sudo zpool scrub tanque
```
Puedes ver el progreso con `zpool status`. No detiene el sistema, pero puede ralentizarlo un poco.

### El Resilvering (Reconstrucción)
Si un disco muere y lo reemplazas:
1.  Sacas el disco malo.
2.  Metes el nuevo.
3.  Comando de reemplazo:
    ```bash
    $ sudo zpool replace tanque /dev/sdb /dev/sde
    ```
    *(Reemplaza el viejo sdb por el nuevo sde).*

ZFS inicia el "Resilvering". Copia los datos necesarios al nuevo disco.
A diferencia de RAID clásico (que copia el disco entero bloque a bloque, incluso el espacio vacío), ZFS **solo copia los datos reales**. Si tienes un disco de 4TB pero solo 100GB usados, el resilvering tardará minutos, no horas.

@section: 7. ARC y L2ARC: Caché Inteligente

ZFS no usa la caché de Linux normal. Usa la suya: **ARC (Adaptive Replacement Cache)**.
Es muy inteligente. Aprende qué datos usas más (frecuencia) y qué datos has usado hace poco (recencia) para mantenerlos en RAM.

**El precio de la magia:** ZFS ama la RAM. Se dice que necesitas 1GB de RAM por cada 1TB de almacenamiento para un rendimiento óptimo (aunque funciona con menos).

### L2ARC (Level 2 ARC)
Si no tienes más RAM pero quieres velocidad, puedes añadir un SSD rápido como caché de lectura.
Esto se llama L2ARC.
```bash
$ sudo zpool add tanque cache /dev/sdf
```
ZFS usará ese SSD para guardar los datos más leídos, liberando carga de los discos mecánicos lentos.

### ZIL y SLOG (Caché de Escritura)
Para bases de datos síncronas, cada escritura debe confirmarse en disco. Esto es lento en discos mecánicos.
Puedes añadir un SSD (o mejor, un Optane) como dispositivo de Log dedicado (SLOG).
```bash
$ sudo zpool add tanque log /dev/sdg
```
Las escrituras van a la velocidad del rayo al SSD (Log) y luego ZFS las vuelca con calma a los discos mecánicos.

@section: 8. Btrfs: El Contendiente Nativo

ZFS es maravilloso, pero Btrfs (B-Tree FS) viene dentro del Kernel de Linux. Eso facilita las cosas. Es el sistema por defecto de Fedora, SUSE y Synology.

Comparte el 90% del ADN con ZFS (CoW, Checksums, Snapshots), pero es más flexible.

### Instalación
Suele venir instalado. Necesitas las herramientas:
```bash
$ sudo apt install btrfs-progs
```

### Creación de un Sistema Btrfs
Btrfs no tiene una capa de "pool" separada estrictamente. El sistema de archivos *es* el pool.

**Crear un RAID 1 (Espejo) de datos y metadatos:**
```bash
$ sudo mkfs.btrfs -m raid1 -d raid1 /dev/sdb /dev/sdc
```
*   `-m raid1`: Metadatos en espejo.
*   `-d raid1`: Datos en espejo.

**Montar:**
Puedes montar cualquiera de los discos, Btrfs sabe que son parte de un equipo.
```bash
$ sudo mount /dev/sdb /mnt/datos
```

### Flexibilidad Extrema: Conversión en Vivo
Esto es algo que ZFS no puede hacer (fácilmente).
Tienes un disco ext4 con datos. Quieres pasarlo a btrfs.
```bash
$ sudo btrfs-convert /dev/sdb1
```
¡Convierte el sistema de archivos manteniendo los datos!

O tienes un solo disco en Btrfs. Compras otro. Puedes convertir tu disco "Single" a "RAID 1" **en caliente, con el sistema montado y usándose**.
```bash
$ sudo btrfs device add /dev/sdc /mnt/datos
$ sudo btrfs balance start -dconvert=raid1 -mconvert=raid1 /mnt/datos
```
El comando `balance` redistribuye los datos entre los discos antiguos y nuevos para cumplir el nivel RAID deseado. Magia pura.

### Subvolúmenes Btrfs
En lugar de Datasets, Btrfs tiene **Subvolúmenes**.
Se comportan como carpetas, pero se pueden montar independientemente.

```bash
$ sudo btrfs subvolume create /mnt/datos/proyectos
```
Verás una carpeta `proyectos`.
Pero puedes hacer snapshots de ella:
```bash
$ sudo btrfs subvolume snapshot /mnt/datos/proyectos /mnt/datos/proyectos_snap
```

### El Talón de Aquiles de Btrfs
A día de hoy (2025), **RAID 5 y RAID 6 en Btrfs siguen considerándose inestables** para producción crítica debido al problema del "Write Hole" (agujero de escritura si se va la luz).
Para RAID 0, 1 y 10, es roca sólida. Pero si necesitas RAID 5/6, usa ZFS o `mdadm` + Btrfs encima.

@quiz: ¿Cuál es la principal ventaja de flexibilidad de Btrfs sobre ZFS en cuanto a la gestión de discos físicos?
@option: Btrfs es más rápido en discos NVMe.
@correct: Btrfs permite añadir discos de diferentes tamaños, convertirlos a RAID y rebalancear los datos en caliente, mientras que ZFS es más rígido con la estructura de sus VDEVs una vez creados.
@option: Btrfs no necesita RAM.
@option: Btrfs soporta compresión y ZFS no.

@section: 9. ZFS Send/Receive: El Backup Definitivo

Imagina hacer un backup de 10TB.
*   **rsync:** Tiene que recorrer millones de archivos, ver fechas, comparar... tarda horas solo en saber qué copiar.
*   **ZFS Send:** Sabe *exactamente* qué bloques han cambiado porque es un sistema CoW.

Puedes enviar un snapshot de una máquina a otra por SSH. Es un stream de bits.

**Comando:**
```bash
$ sudo zfs send tanque/proyectos@hoy | ssh usuario@servidor_backup zfs recv backup_pool/proyectos_mirror
```
Esto envía **solo las diferencias** (deltas) entre el último snapshot y "hoy". Es increíblemente eficiente. Puedes replicar servidores enteros cada 5 minutos con impacto casi nulo.

@section: 10. Resumen y Comparativa

| Característica | ZFS (OpenZFS) | Btrfs |
| :--- | :--- | :--- |
| **Origen** | Enterprise (Sun/Oracle). | Linux Nativo (Oracle/SUSE/Facebook). |
| **Estabilidad** | Legendaria. Indestructible. | Muy buena (excepto RAID 5/6). |
| **RAM** | Hambriento (ARC). Necesita mucha. | Ligero. |
| **Facilidad de uso** | Curva de aprendizaje media. Comandos claros. | Comandos algo más complejos (`subvolume`, `balance`). |
| **Flexibilidad** | Rígido (difícil quitar discos). | Muy flexible (añadir/quitar en caliente). |
| **Deduplicación** | Existe, pero consume RAM monstruosa (Evitar). | Out-of-band (herramientas como `bees` o `duperemove`). |
| **Uso Ideal** | Servidores de archivos masivos, Bases de Datos, Virtualización. | Escritorios (Fedora), Contenedores, Servidores generales. |

**Conclusión del Experto:**
*   Si construyes un **Servidor de Almacenamiento (NAS)** dedicado: Usa **ZFS** (TrueNAS usa ZFS). La integridad de datos es lo primero.
*   Si instalas un **Linux de Escritorio o Servidor General**: Usa **Btrfs** para `/` y `/home`. Te permite hacer snapshots antes de actualizaciones (con herramientas como Timeshift o Snapper) y salvar tu sistema si una actualización sale mal.

Ahora posees el conocimiento para proteger los datos contra la corrupción, el tiempo y los fallos de hardware. Eres un Guardián de los Datos.