@title: Rendimiento del sistema: CPU, memoria, I/O y red
@icon: 📊
@description: Enfoque USE y herramientas sar, vmstat, iostat y strace para localizar cuellos de botella en servidores Linux.
@order: 1

# Análisis de rendimiento en Linux: métricas y herramientas

Usarás un enfoque sistemático (p. ej. **USE**: utilización, saturación, errores) para decidir si el cuello de botella es **CPU, memoria, disco o red**, apoyándote en herramientas como **sar**, **vmstat**, **iostat** y **strace** típicas de LPIC-2 y de taller de incidentes.

@section: Mapa LPIC-2 — Módulo 4 (rendimiento, virtualización, HA, recuperación)

Alineación con **LPIC-2** en **capacidad, virtualización y continuidad**:

*   **200.x / 201.x — Diagnóstico:** CPU, memoria, I/O, red; `sar`, `iostat`, `vmstat`, `strace`.
*   **Virtualización:** KVM/libvirt, ajuste de recursos de invitados.
*   **Alta disponibilidad:** conceptos de quorum, fencing, Pacemaker (intro sólida en lección HA).
*   **Copias de seguridad y recuperación:** estrategias 3-2-1, pruebas de restore.
*   **RHEL:** `tuned`, perfiles de rendimiento; **cualquier distro:** metodología USE igual.

"El servidor va lento".
Esa es la frase más temida y vaga que recibirás. Tu trabajo no es reiniciar el servidor. Tu trabajo es responder: **¿Por qué?**
¿Es la CPU? ¿Es el disco? ¿Es la red? ¿Es la base de datos bloqueando tablas?

En este módulo, vamos a ir más allá de mirar `top`. Vamos a aprender la metodología de análisis **USE** (Utilization, Saturation, Errors), entenderemos cómo funciona la memoria virtual de Linux y usaremos herramientas forenses para encontrar la causa raíz.

@section: 1. Metodología USE (Brendan Gregg)

No mires métricas al azar. Sigue un sistema para cada recurso (CPU, Memoria, Disco, Red).

Para cada recurso, pregunta:
1.  **Utilization (Utilización):** ¿Cuánto tiempo está ocupado el recurso? (ej: CPU al 90%).
2.  **Saturation (Saturación):** ¿Hay trabajo encolado que no puede ser procesado? (ej: Load Average alto, peticiones de disco en espera).
3.  **Errors (Errores):** ¿Hay fallos físicos o lógicos? (ej: paquetes de red descartados, errores de I/O de disco).

@section: 2. CPU: El Cerebro

### Conceptos Clave
*   **User Time:** Tiempo gastado en tus programas (PHP, Java, MySQL). Si es alto, tu código es pesado.
*   **System Time (Kernel):** Tiempo gastado en llamadas al sistema (abrir archivos, gestionar red). Si es alto, algo raro pasa (drivers malos, demasiadas syscalls).
*   **Nice Time:** Tiempo en procesos de baja prioridad.
*   **Idle:** Tiempo libre.
*   **I/O Wait (wa):** **¡VITAL!** Tiempo que la CPU está parada, sin hacer nada, esperando a que el disco responda. **Si ves Wait alto, el problema NO es la CPU, es el DISCO.**
*   **Steal Time (st):** Solo en máquinas virtuales. Tiempo que tu máquina virtual quería correr pero el Hipervisor físico no le dejó (porque el servidor físico está sobrevendido).

### Herramientas: `vmstat`
`vmstat` te da todo en una línea. Ejecuta `vmstat 1` (una línea cada segundo).

```text
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 200000  50000 500000    0    0    10    20  100  200 50 10 35  5  0
```

Columnas vitales:
*   **r (runnable):** Procesos intentando correr. Si `r` > número de núcleos de CPU, tienes **Saturación de CPU**.
*   **b (blocked):** Procesos bloqueados esperando I/O (disco/red). Si es alto, mira el disco.
*   **us/sy/id/wa:** Desglose de uso de CPU.

@quiz: Estás mirando `top` o `vmstat` y ves que la CPU está al 100% de uso, pero el 90% de ese uso está marcado como `wa` (Wait I/O). ¿Qué significa?
@option: Necesitas un procesador más rápido.
@correct: La CPU no es el problema; el sistema es lento porque el almacenamiento (disco) es demasiado lento y la CPU pasa todo el tiempo esperando datos.
@option: Tienes un virus minando criptomonedas.
@option: Te falta memoria RAM.

@section: 3. Memoria: El Mito de "Free"

En Linux, la RAM libre es RAM desperdiciada. Linux usa toda la RAM que no usas para **Caché de Disco**.
Por eso, si haces `free -h`, verás que la columna "free" es muy baja, pero "buff/cache" es alta. **Eso es bueno.**
La métrica real es **"available"**. Esa es la memoria que puedes usar antes de empezar a usar Swap.

### El Peligro del Swapping
Si la memoria se llena de verdad, Linux empieza a usar el disco duro como RAM (Swap). El disco es 100.000 veces más lento que la RAM.
Si ves actividad constante en las columnas `si` (swap in) y `so` (swap out) de `vmstat`, tu servidor está en la **Zona de la Muerte**. El rendimiento caerá en picado.

**El OOM Killer (Out Of Memory Killer):**
Si se acaba la RAM y la Swap, el Kernel entra en pánico para salvar el sistema. Invoca al OOM Killer.
Este asesino busca el proceso que más memoria usa (probablemente tu base de datos) y **lo mata instantáneamente**.
Síntoma: El servidor revive de repente, pero la base de datos se ha caído.
Diagnóstico: `dmesg | grep -i "killed process"`.

@section: 4. I/O de Disco: El Cuello de Botella Eterno

El disco es la parte más lenta. Diagnosticarlo es clave.

### Herramienta: `iostat`
Parte del paquete `sysstat`. Ejecuta `iostat -xz 1`.

```text
Device:  rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
sda        0.00     0.50   10.00   20.00   500.00  1000.00    50.00     0.05    2.50    1.00    4.00   1.00   5.00
```

Métricas vitales:
*   **r/s, w/s:** Lecturas/Escrituras por segundo (IOPS). ¿Está tu disco al límite de sus IOPS físicos? (Un HDD da ~100 IOPS, un SSD SATA ~5000, un NVMe ~200.000).
*   **await:** Tiempo medio (ms) que una petición espera hasta ser servida. Si es > 10ms en un SSD, hay latencia.
*   **avgqu-sz:** Tamaño medio de la cola. Si es > 1, el disco no da abasto (Saturación).
*   **%util:** Porcentaje de tiempo que el disco estuvo ocupado. Si está cerca del 100% constantemente, el disco es el cuello de botella.

### Herramienta: `iotop`
Si sabes que el disco está lento pero no sabes *quién* está escribiendo, usa `sudo iotop`.
Te muestra una lista de procesos ordenada por uso de disco en tiempo real. (Igual que `top` pero para I/O).

@section: 5. Red: La Autopista

### Herramienta: `sar -n DEV`
Muestra estadísticas de red históricas o en tiempo real.
`sar -n DEV 1`

*   **rxpck/s, txpck/s:** Paquetes por segundo.
*   **rxkB/s, txkB/s:** Kilobytes por segundo.

Si `rxpck/s` es altísimo pero `rxkB/s` es bajo, estás recibiendo un ataque de paquetes pequeños (DDoS o fallo de red).
Si el tráfico se acerca al límite de tu tarjeta (1Gbps = ~125.000 kB/s), tienes saturación de ancho de banda.

@section: 6. Análisis Profundo: `strace`

¿Qué pasa cuando un proceso consume 100% CPU pero no sabes qué hace? ¿O se queda colgado y no responde?
**`strace`** es la visión de rayos X. Intercepta las **Llamadas al Sistema**.

Ejemplo: Tu servidor web apache está colgado (PID 1234).
```bash
$ sudo strace -p 1234
```
Verás en tiempo real lo que hace:
`open("/var/www/html/index.php", O_RDONLY) = 5`
`read(5, "<?php ...", 4096) = 4096`
`connect(3, {sa_family=AF_INET, sin_port=htons(3306)...`

Si ves que se queda parado en un `connect` a una IP, sabes que está esperando a la base de datos o a una API externa. `strace` te dice *exactamente* dónde se atasca un programa.

**Advertencia:** `strace` ralentiza mucho el proceso. Úsalo solo para diagnóstico breve.

@section: 7. Resumen de Herramientas (El Cinturón de Batman)

| Síntoma | Herramienta Rápida | Herramienta Profunda | Qué buscar |
| :--- | :--- | :--- | :--- |
| **Lentitud General** | `top`, `htop` | `vmstat 1` | Load Avg > Núcleos, Wait I/O alto. |
| **CPU Alta** | `top` | `perf`, `strace` | Procesos en 'R', User vs System time. |
| **Memoria** | `free -m` | `vmstat`, `ps_mem` | Swap in/out, OOM killer en logs. |
| **Disco Lento** | `iostat -xz 1` | `iotop`, `blktrace` | %util alto, await alto, queue size. |
| **Red** | `iftop`, `nload` | `sar -n DEV`, `tcpdump` | Saturación ancho de banda, errores. |

Dominar el análisis de rendimiento es lo que diferencia a un "rebooter" de un verdadero Ingeniero de Sistemas.