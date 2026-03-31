@title: E/S: bloque vs caracter, controladores y DMA
@icon: 🔌
@description: Syscalls read/write, schedulers de disco, NVMe y latencias.
@order: 5

# Entrada/salida: del syscall al cable

La **E/S** conecta CPU con discos, red y periféricos. **Block devices** (512B–4KiB sectores) vs **character** streams. **DMA** libera CPU moviendo datos a buffers. Esta lección resume **schedulers** (mq-deadline, bfq), **NVMe** colas paralelas, y **epoll/select** para sockets.

@section: Syscalls

`read`/`write` copian entre espacio usuario y kernel; **zero-copy** (`sendfile`) evita copias extra.

@section: Disco

**NCQ** reordena peticiones; **SSDs** cambian el juego (latencia baja, desgaste por escritura). **TRIM** informa bloques libres.

@section: Red

**Buffers** y **backpressure** en sockets; **NAPI** en Linux para interrupciones de red.

@section: Errores frecuentes

* **Synchronous writes** masivos sin batching.
* Abrir demasiados fds sin `close`.

@section: Laboratorio sugerido

1. Mide IOPS con `fio` en modo aleatorio vs secuencial.
2. Observa `iostat -x 1` bajo carga.
3. Escribe un servidor `epoll` mínimo y compara con uno por hilo.

@quiz: ¿Qué mecanismo permite a dispositivos transferir datos a memoria sin ocupar la CPU en cada byte?
@option: IRQ exclusivamente
@correct: DMA (acceso directo a memoria)
@option: Mutex
