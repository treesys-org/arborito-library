
@title: Almacenamiento avanzado: RAID por software y LVM
@icon: 💽
@description: mdadm para RAID, capas PV/VG/LV, snapshots y aprovisionamiento fino frente a particiones fijas en servidores.
@order: 3

# RAID por software y LVM: volúmenes flexibles en Linux

Bienvenido al módulo donde se separan los aficionados de los profesionales.

En el nivel Junior, aprendiste a instalar Linux en un disco duro. Creaste una partición `/`, quizás una `/home` y una `swap`. Usaste `fdisk` o `parted`. Eso está muy bien para tu portátil personal o para una máquina virtual de pruebas.

Pero en el mundo real, en el centro de datos, **el almacenamiento es un organismo vivo**.

Imagina estas situaciones, que ocurren todos los días en la vida de un SysAdmin:

1.  **El Crecimiento Inesperado:** Tienes un servidor de base de datos con un disco de 500GB. Se suponía que duraría 3 años. A los 6 meses, Marketing lanza una campaña exitosa y el disco se llena. El servidor se para. Tu jefe te mira. ¿Qué haces? ¿Apagas el servidor, clonas el disco a uno de 1TB y rezas para que arranque? ¿Y si no puedes apagar el servidor?
2.  **El Fallo Físico:** Tienes un servidor de archivos crítico. Un martes a las 3 de la mañana, un disco duro mecánico decide morir. Hace "clic-clic-clic" y desaparece. ¿Has perdido los datos de la empresa? ¿Tienes que restaurar un backup de ayer y perder el trabajo de hoy?
3.  **La Migración en Caliente:** Tienes un servidor viejo con discos lentos. Has comprado discos SSD nuevos. Quieres mover todo el sistema operativo y los datos a los SSDs **mientras** los usuarios siguen trabajando, sin que se den cuenta.

Con particiones tradicionales (estáticas), estos problemas son pesadillas logísticas.
Con **LVM (Logical Volume Manager)** y **RAID (Redundant Array of Independent Disks)**, estos problemas se resuelven con unos pocos comandos, a menudo sin reiniciar la máquina.

En esta guía masiva, vamos a convertirte en un arquitecto de almacenamiento. Aprenderás a combinar discos físicos para que actúen como uno solo, a crear espejos de seguridad, a expandir sistemas de archivos en caliente y a viajar en el tiempo con snapshots.

@section: 1. RAID: La Fortaleza de los Discos

Empecemos por la base física. Los discos duros (y los SSDs) son, por naturaleza, componentes no fiables. Tienen partes mecánicas o celdas de memoria que se degradan. **Van a fallar**. No es una cuestión de "si", sino de "cuándo".

**RAID** (Redundant Array of Independent Disks - Matriz Redundante de Discos Independientes) es una tecnología que nos permite agrupar varios discos físicos para que el sistema operativo los vea como una sola unidad lógica.

Dependiendo de cómo los agrupemos (el "Nivel" de RAID), obtenemos:
*   **Redundancia:** Si un disco muere, no perdemos datos.
*   **Velocidad:** Leemos/Escribimos en varios discos a la vez.
*   **Capacidad:** Sumamos el tamaño de varios discos.

### Hardware RAID vs. Software RAID
En los servidores caros antiguos, se usaban tarjetas controladoras RAID físicas. Eran caras y tenían un defecto grave: si la tarjeta se rompía, necesitabas *otra tarjeta idéntica* (mismo modelo y firmware) para leer los datos. Si el fabricante había cerrado, estabas muerto.

En Linux moderno, usamos **Software RAID** mediante la herramienta `mdadm` (Multiple Device Admin).
*   **Ventaja 1:** Es agnóstico del hardware. Puedes sacar los discos de un servidor HP, pincharlos en un PC clónico barato, y Linux reconocerá el RAID y montará los datos.
*   **Ventaja 2:** Es gratis y extremadamente potente y rápido en CPUs modernas.

### Los Niveles de RAID Explicados (La Teoría Vital)

Antes de teclear, debes saber qué arquitectura vas a construir.

#### RAID 0 (Stripe - Rayado)
*   **Concepto:** La información se divide en trozos y se escribe alternativamente en los discos.
*   **Requisitos:** Mínimo 2 discos.
*   **Velocidad:** Extrema. (Velocidad de 1 disco * N discos).
*   **Capacidad:** Suma total (100%).
*   **Redundancia:** **CERO**.
*   **El Peligro:** Si falla **UN SOLO** disco, pierdes **TODOS** los datos del array. Es estadísticamente el doble de inseguro que un disco solo.
*   **Uso:** Cachés temporales, renderizado de vídeo donde los datos originales están en otro lado. Nunca para guardar nada importante.

#### RAID 1 (Mirror - Espejo)
*   **Concepto:** Todo lo que se escribe en el Disco A, se copia idénticamente en el Disco B.
*   **Requisitos:** Mínimo 2 discos.
*   **Velocidad:** Escritura normal (o algo lenta), Lectura rápida (puede leer de ambos a la vez).
*   **Capacidad:** 50%. (Dos discos de 1TB te dan 1TB útil).
*   **Redundancia:** Alta. Puede fallar un disco y el sistema sigue funcionando.
*   **Uso:** Sistema operativo (/boot, /), bases de datos críticas pequeñas.

#### RAID 5 (Paridad Distribuida)
*   **Concepto:** Los datos se escriben en varios discos, y se calcula un bloque de "Paridad" (una suma matemática). La paridad se reparte entre todos los discos.
*   **Requisitos:** Mínimo 3 discos.
*   **Capacidad:** (N-1) discos. (3 discos de 1TB = 2TB útiles).
*   **Redundancia:** Puede fallar 1 disco cualquiera. Los datos se reconstruyen matemáticamente usando los otros y la paridad.
*   **Uso:** Almacenamiento de archivos general.
*   **Desventaja:** La escritura es lenta (hay que calcular paridad). Si falla un disco, el rendimiento cae en picado hasta que se reemplaza y se "reconstruye" (lo cual tarda mucho y estresa a los discos restantes).

#### RAID 6 (Doble Paridad)
*   **Concepto:** Como RAID 5, pero con dos bloques de paridad distintos.
*   **Requisitos:** Mínimo 4 discos.
*   **Redundancia:** Pueden fallar **2 discos** a la vez.
*   **Uso:** Archivos de gran capacidad donde la seguridad es vital.

#### RAID 10 (1+0 - Espejo de Rayados)
*   **Concepto:** Un híbrido. Haces parejas de espejos (RAID 1) y luego unes esas parejas en un Stripe (RAID 0).
*   **Requisitos:** Mínimo 4 discos.
*   **Capacidad:** 50%.
*   **Velocidad:** Muy alta (como RAID 0).
*   **Redundancia:** Muy alta (como RAID 1).
*   **Uso:** El estándar de oro para bases de datos de alto rendimiento y virtualización. Es caro, pero es lo mejor.

@quiz: Tienes un servidor con 4 discos de 2TB cada uno. Necesitas maximizar la velocidad de lectura/escritura para una caché temporal de procesamiento de video, y si se pierden los datos no pasa nada porque se pueden volver a descargar. ¿Qué nivel de RAID eliges?
@option: RAID 1
@option: RAID 5
@correct: RAID 0
@option: RAID 6

@section: 2. Laboratorio RAID: Manos a la Obra con `mdadm`

Vamos a simular que somos administradores. No necesitas discos físicos reales para practicar; podemos usar archivos o discos virtuales en una VM.
Asumiremos que tienes dos discos vacíos disponibles: `/dev/sdb` y `/dev/sdc`.

**Paso 0: Instalación**
En Debian/Ubuntu:
```bash
$ sudo apt update
$ sudo apt install mdadm
```

**Paso 1: Preparar los discos**
Aunque puedes hacer RAID sobre discos "crudos" (raw), es buena práctica particionarlos y marcar la partición como "Linux RAID autodetect".
```bash
$ sudo fdisk /dev/sdb
# (Crea una partición nueva 'n', primaria 'p', todo el disco)
# (Cambia el tipo 't' a 'fd' - Linux RAID auto)
# (Guarda 'w')
```
Repite para `/dev/sdc`. Ahora tenemos `/dev/sdb1` y `/dev/sdc1`.

**Paso 2: Crear el Array (RAID 1)**
Vamos a crear un dispositivo virtual llamado `/dev/md0` (Multi-Disk 0) que sea un espejo de los dos discos.

```bash
$ sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
```
El sistema te preguntará si estás seguro. Di `yes`.
*   `--create`: Crear nuevo array.
*   `--level=1`: Tipo RAID 1 (Mirror).
*   `--raid-devices=2`: Cuántos discos activos forman el array.

**Paso 3: Verificar el estado**
Inmediatamente después de crearlo, el kernel empezará a sincronizar los discos (copiar ceros de uno a otro para asegurar que son idénticos).
Para ver el proceso, miramos el archivo sagrado `/proc/mdstat`:

```bash
$ cat /proc/mdstat
Personalities : [raid1]
md0 : active raid1 sdc1[1] sdb1[0]
      1047552 blocks super 1.2 [2/2] [UU]
      [=>...................]  resync =  5.7% (60000/1047552) finish=1.5min speed=10000K/sec
```
*   `[2/2]`: Esperamos 2 discos y tenemos 2.
*   `[UU]`: Los dos discos están **U**p (Arriba/Funcionando). Si vieras `[_U]`, uno estaría roto o perdido.
*   `resync`: La barra de progreso.

También puedes usar:
```bash
$ sudo mdadm --detail /dev/md0
```
Esto te da un informe detallado forense del array.

**Paso 4: Crear el Sistema de Archivos**
Ahora `/dev/md0` se comporta exactamente igual que un disco normal.
```bash
$ sudo mkfs.ext4 /dev/md0
```

**Paso 5: Montar y Usar**
```bash
$ sudo mkdir /mnt/datos_seguros
$ sudo mount /dev/md0 /mnt/datos_seguros
```
¡Listo! Todo lo que guardes en `/mnt/datos_seguros` se está escribiendo en dos discos físicos simultáneamente.

**Paso 6: La Persistencia (Crucial)**
Si reinicias el ordenador ahora, es posible que el RAID no arranque o cambie de nombre a `/dev/md127`. Tienes que guardar la configuración.
```bash
$ sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
```
Y luego, vital, actualizar el initramfs (la memoria inicial de arranque) para que sepa del RAID:
```bash
$ sudo update-initramfs -u
```

@section: 3. Simulación de Desastre y Recuperación

Un RAID no sirve de nada si no sabes arreglarlo cuando se rompe. Vamos a simular un fallo.

**El Escenario:** El disco `/dev/sdb1` ha muerto.

**1. Marcar el disco como fallido:**
Como nuestros discos virtuales no se rompen físicamente, le decimos al kernel que finja que se ha roto.
```bash
$ sudo mdadm /dev/md0 --fail /dev/sdb1
```
Si miras `/proc/mdstat`, verás `[2/1] [_U]`. El sistema está degradado, pero **sigue funcionando**. Tus datos están accesibles gracias al otro disco.

**2. Retirar el disco muerto:**
Ahora le decimos al RAID que olvide ese disco para poder sacarlo.
```bash
$ sudo mdadm /dev/md0 --remove /dev/sdb1
```

**3. El Reemplazo Físico:**
(Aquí apagarías el servidor, sacarías el disco malo, pondrías uno nuevo, y encenderías. O lo harías en caliente si el servidor lo soporta).
Supongamos que has puesto un disco nuevo y el sistema lo reconoce como `/dev/sdd`.
Tienes que particionarlo igual que el otro (`fdisk`). Digamos que es `/dev/sdd1`.

**4. Añadir el disco nuevo al Array:**
```bash
$ sudo mdadm /dev/md0 --add /dev/sdd1
```

**5. La Reconstrucción (Rebuild):**
Automáticamente, el RAID verá un disco nuevo y fresco y empezará a clonar los datos del disco superviviente al nuevo.
Mira `/proc/mdstat` para ver la magia de la recuperación en directo.
Cuando termine, volverás a tener `[UU]`. El sistema está sano de nuevo.

@quiz: Tienes un RAID 1 configurado con dos discos. Uno falla. ¿Qué ocurre con los datos y el servicio?
@option: El servicio se detiene y se pierden datos hasta que reemplaces el disco.
@correct: El servicio continúa funcionando sin interrupción y sin pérdida de datos, pero el array está en modo degradado (sin redundancia).
@option: El sistema se apaga automáticamente por seguridad.
@option: El RAID se convierte automáticamente en RAID 0.

@section: 4. LVM: El Administrador de Volúmenes Lógicos

Hemos protegido los discos con RAID. Ahora vamos a gestionar el espacio con **LVM**.
LVM es una capa de abstracción que se pone *encima* de los discos físicos (o del RAID) y *debajo* del sistema de archivos.

### El Vocabulario de LVM
Para entender LVM, piensa en construcciones de LEGO.

1.  **PV (Physical Volume - Volumen Físico):** Es el ladrillo base. Puede ser un disco duro (`/dev/sda`), una partición (`/dev/sdb1`) o, lo que es mejor, un dispositivo RAID (`/dev/md0`). LVM "marca" estos dispositivos para usarlos.
2.  **VG (Volume Group - Grupo de Volúmenes):** Es la "Piscina" o el montón de piezas de LEGO. Agrupamos uno o más PVs en un VG.
    *   Ejemplo: Tienes un disco de 1TB y otro de 2TB. Creas un VG llamado `servidor_vg`. Ahora tienes una piscina de almacenamiento de 3TB. Al sistema ya no le importa qué disco es cuál. Solo ve "3TB de espacio libre".
3.  **LV (Logical Volume - Volumen Lógico):** Es lo que construyes con las piezas. Es la partición virtual.
    *   Sacas 50GB de la piscina para crear `lv_root`.
    *   Sacas 500GB para `lv_home`.
    *   Lo mágico es que los LVs son elásticos. ¿`lv_root` se llena? Si queda espacio en la piscina (VG), puedes agrandar el LV en caliente.

### Diagrama Mental
`Disco Físico (PV)` -> `Grupo (VG)` -> `Volumen Lógico (LV)` -> `Sistema de Archivos (ext4/xfs)`

@section: 5. Laboratorio LVM: Creación y Expansión

Vamos a crear un sistema LVM desde cero y luego a simular que nos quedamos sin espacio.
Usaremos nuestro dispositivo RAID `/dev/md0` como base (PV), para tener un sistema robusto y flexible.

### Fase 1: Creación

**1. Inicializar el Volumen Físico (PV):**
"Marcamos" el dispositivo RAID para que LVM lo use.
```bash
$ sudo pvcreate /dev/md0
Physical volume "/dev/md0" successfully created.
```

**2. Crear el Grupo de Volúmenes (VG):**
Llamaremos a nuestro grupo `vg_datos`.
```bash
$ sudo vgcreate vg_datos /dev/md0
Volume group "vg_datos" successfully created
```
Verifica el estado con `sudo vgs`. Verás que tienes un VG con el tamaño total del RAID libre.

**3. Crear Volúmenes Lógicos (LV):**
Vamos a crear dos volúmenes: uno para proyectos y otro para backups.
```bash
$ sudo lvcreate -L 10G -n lv_proyectos vg_datos
$ sudo lvcreate -L 20G -n lv_backups vg_datos
```
*   `-L 10G`: Tamaño fijo de 10 Gigabytes.
*   `-n nombre`: Nombre del volumen.
*   `vg_datos`: De qué grupo sacamos el espacio.

Ahora existen dos dispositivos nuevos en `/dev/mapper/`:
*   `/dev/mapper/vg_datos-lv_proyectos`
*   `/dev/mapper/vg_datos-lv_backups`

**4. Formatear y Montar:**
Esto es igual que siempre.
```bash
$ sudo mkfs.ext4 /dev/mapper/vg_datos-lv_proyectos
$ sudo mkfs.ext4 /dev/mapper/vg_datos-lv_backups
$ sudo mkdir /mnt/proyectos /mnt/backups
$ sudo mount /dev/mapper/vg_datos-lv_proyectos /mnt/proyectos
$ sudo mount /dev/mapper/vg_datos-lv_backups /mnt/backups
```

### Fase 2: La Expansión (El escenario "¡Disco Lleno!")

Alarma. El equipo de desarrollo dice que `/mnt/proyectos` (10GB) está lleno. Necesitan 5GB más. Tienes espacio libre en el grupo `vg_datos`.
¿Cómo lo haces sin apagar el servidor y sin desmontar el disco?

**1. Extender el Volumen Lógico (LV):**
Le decimos a LVM que añada 5GB al contenedor.
```bash
$ sudo lvextend -L +5G /dev/vg_datos/lv_proyectos
```
El mensaje dirá que el volumen lógico ha cambiado de 10GB a 15GB.
**¡PERO OJO!** Si haces `df -h`, verás que sigue teniendo 10GB.
¿Por qué?
Porque has agrandado "la habitación", pero no has agrandado "la alfombra" (el sistema de archivos ext4). El sistema de archivos no sabe que el disco de abajo ha crecido.

**2. Redimensionar el Sistema de Archivos:**
Tenemos que decirle a ext4 que se expanda para ocupar el nuevo espacio.
```bash
$ sudo resize2fs /dev/vg_datos/lv_proyectos
```
*(Nota: Si usas el sistema de archivos XFS, el comando es `xfs_growfs /mnt/proyectos`).*

¡Hecho! Haz `df -h`. Verás 15GB disponibles. El servidor no ha parado ni un segundo.

### Fase 3: Añadir más discos físicos

¿Y si se llena el Grupo de Volúmenes (la piscina)?
Imagina que `vg_datos` está lleno. Compras un disco nuevo `/dev/sdd`.

1.  Creas PV: `sudo pvcreate /dev/sdd`.
2.  Extiendes el VG: `sudo vgextend vg_datos /dev/sdd`.
    *   ¡Ahora tu piscina es más grande!
3.  Extiendes los LVs como hicimos antes.

Esta capacidad de agregar discos arbitrarios a un grupo es lo que hace a LVM tan poderoso.

@quiz: Has ejecutado `lvextend` para añadir 10GB a un volumen lógico, pero `df -h` sigue mostrando el tamaño antiguo. ¿Qué paso te falta?
@option: Reiniciar el servidor.
@option: Volver a montar la partición.
@correct: Redimensionar el sistema de archivos (con `resize2fs` para ext4 o `xfs_growfs` para XFS).
@option: Ejecutar `pvcreate`.

@section: 6. Snapshots LVM: La Máquina del Tiempo

Una de las funciones más potentes de LVM es el **Snapshot** (Instantánea).
Un snapshot no es una copia de seguridad completa (no duplica los datos). Es una "foto" del estado del disco en un momento dado usando una técnica llamada **Copy-on-Write (CoW)**.

*   Creas un snapshot. Inicialmente ocupa casi 0 espacio.
*   LVM se queda vigilando.
*   Cuando algo intenta modificar un bloque de datos en el disco original, LVM hace una copia del bloque *original* (como estaba antes del cambio) y lo guarda en el área del snapshot.
*   El snapshot crece a medida que cambias el original.

**Uso práctico: Actualizaciones Peligrosas**
Vas a actualizar el servidor y tienes miedo de que se rompa.

1.  **Crear Snapshot:**
    ```bash
    $ sudo lvcreate -L 2G -s -n snap_antes_update /dev/vg_datos/lv_proyectos
    ```
    *   `-s`: Snapshot.
    *   `-L 2G`: Reservamos 2GB para guardar los cambios (deltas). Si cambiamos más de 2GB de datos en el original, el snapshot se romperá.

2.  **Hacer cambios:**
    Instalas actualizaciones, borras archivos, rompes cosas en `/mnt/proyectos`.

3.  **Comprobar el desastre:**
    Todo está roto. Quieres volver atrás.

4.  **Restaurar (Merge):**
    Para restaurar, tienes que desmontar el volumen original.
    ```bash
    $ sudo umount /mnt/proyectos
    $ sudo lvconvert --merge /dev/vg_datos/snap_antes_update
    ```
    LVM coge los bloques originales guardados en el snapshot y los devuelve a su sitio. El snapshot desaparece (se autoconsume).

5.  **Montar de nuevo:**
    `sudo mount /dev/mapper/vg_datos-lv_proyectos /mnt/proyectos`.
    ¡Todo está exactamente igual que antes de empezar!

@section: 7. Thin Provisioning: La Ilusión de la Abundancia

En entornos de virtualización (como cuando creas VPS para clientes), a menudo quieres "sobrevender" espacio.
Tienes 1TB de disco real. Quieres crear 20 máquinas virtuales y decirle a cada una que tiene 100GB de disco.
Total prometido: 20 * 100 = 2TB.
Total real: 1TB.

Esto se llama **Thin Provisioning**.
Creas un "Thin Pool". Los volúmenes dentro de él no ocupan el espacio que dicen tener. Solo ocupan el espacio de los datos que *realmente* han escrito.
Si el usuario cree que tiene 100GB pero solo ha guardado 2GB de fotos, en tu disco físico solo gasta 2GB.

**Comandos:**
```bash
# Crear un Thin Pool
$ sudo lvcreate -L 500G -T vg_datos/thinpool

# Crear un volumen Thin dentro
$ sudo lvcreate -V 100G -T vg_datos/thinpool -n vm_cliente1
```
El cliente ve un disco de 100GB. Tú gastas 0.

**El Riesgo:** Si todos los clientes deciden llenar sus discos a la vez y superas el 1TB físico, el sistema se bloquea. Tienes que monitorizarlo de cerca.

@section: 8. Estrategia de Particionado Recomendada para Servidores

Si vas a montar un servidor profesional, este es el esquema ganador:

1.  **Discos Físicos:** 2 discos idénticos (ej: `/dev/sda`, `/dev/sdb`).
2.  **Partición de Arranque (EFI/Boot):** Fuera de LVM/RAID (o en RAID 1 simple para metadata vieja). Normalmente una partición pequeña de 512MB al principio de cada disco.
3.  **RAID:** Crea un RAID 1 (`/dev/md0`) con el resto del espacio de ambos discos.
4.  **LVM:**
    *   PV: `/dev/md0`.
    *   VG: `system_vg`.
    *   LV `root`: 20GB para `/`.
    *   LV `swap`: 4GB (o lo que necesites).
    *   LV `var`: 20GB para `/var` (logs, docker, bases de datos).
    *   LV `home`: El resto (o déjalo libre para asignar luego).

Esta configuración te da redundancia (si falla un disco, sigues vivo) y flexibilidad (puedes expandir `/var` si los logs se llenan).

@quiz: Estás configurando un servidor crítico y quieres usar RAID 5 por software con `mdadm`. Tienes 3 discos de 1TB. ¿Cuánto espacio útil tendrás disponible aproximadamente para datos?
@option: 1TB
@correct: 2TB (N-1 discos)
@option: 3TB
@option: 1.5TB

@section: Resumen / Cheat Sheet

**RAID (`mdadm`):**
*   Crear: `mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1`
*   Ver estado: `cat /proc/mdstat`
*   Detalles: `mdadm --detail /dev/md0`
*   Fallar disco: `mdadm /dev/md0 --fail /dev/sdb1`
*   Quitar disco: `mdadm /dev/md0 --remove /dev/sdb1`
*   Añadir disco: `mdadm /dev/md0 --add /dev/sdb1`

**LVM:**
*   PV (Físico): `pvcreate`, `pvs`, `pvdisplay`.
*   VG (Grupo): `vgcreate`, `vgs`, `vgextend`.
*   LV (Lógico): `lvcreate`, `lvs`, `lvextend`, `lvresize`.
*   Snapshot: `lvcreate -s`.
*   Sistema de Archivos: `resize2fs` (ext4), `xfs_growfs` (xfs).

Ahora eres el dueño de tus discos, no su esclavo. Puedes adaptar el almacenamiento a las necesidades del negocio en tiempo real.