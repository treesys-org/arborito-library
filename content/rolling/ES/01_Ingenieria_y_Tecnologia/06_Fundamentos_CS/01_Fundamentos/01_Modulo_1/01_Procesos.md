@title: Procesos: PCB, estados y planificación
@icon: 🧠
@description: Creación, cambio de contexto, colas y métricas de planificador.
@order: 1

# Procesos: la unidad de ejecución aislada

Un **proceso** es un programa en ejecución con su propio espacio de direcciones y recursos del sistema operativo. El kernel mantiene un **PCB** (Process Control Block) con PID, registros, tablas de memoria, descriptores de archivo y estado. Esta lección conecta **fork/exec**, **estados** (new, ready, running, blocked, terminated) y **planificación** (FCFS, SJF, round-robin, prioridades).

@section: Creación en Unix

`fork()` clona el proceso; `exec()` reemplaza la imagen. El hijo hereda descriptores con políticas de copia (y **COW** en memoria). **Zombies** aparecen si el padre no hace `wait()`.

@section: Hilos vs procesos

Los **hilos** comparten el espacio de direcciones del proceso; cambio de contexto más barato pero más acoplamiento. Los procesos aíslan fallos pero cuestan más crear IPC.

@section: Planificación

**Preemptive** multitasking interrumpe quantum; **priority inversion** ocurre si un hilo de baja prioridad retiene un lock que un hilo de alta necesita (mitigar con **priority inheritance** en RTOS).

@section: Métricas

**Throughput**, **latencia**, **tiempo de respuesta** y **utilización** de CPU miden el planificador; no hay métrica única para todos los workloads.

@section: Laboratorio sugerido

1. En Linux, observa `/proc/<pid>/status` y `ps -o pid,ppid,cmd`.
2. Escribe un programa que haga fork y mide tiempo de creación.
3. Compara `pthread` vs proceso para una tarea CPU-bound.

@quiz: ¿Qué estructura mantiene el kernel con metadatos de un proceso?
@option: inode
@correct: PCB / estructura de control de proceso
@option: TLB
