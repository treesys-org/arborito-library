@title: Processes: PCB, States, and Scheduling
@icon: 🧠
@description: Creation, context switching, queues, and scheduler metrics.
@order: 1

# Processes: the isolated unit of execution

A **process** is a program in execution with its own address space and OS resources. The kernel keeps a **PCB** (Process Control Block) with PID, registers, memory tables, file descriptors, and state. This lesson connects **fork/exec**, **states** (new, ready, running, blocked, terminated), and **scheduling** (FCFS, SJF, round-robin, priorities).

@section: Creation on Unix

`fork()` clones the process; `exec()` replaces the image. The child inherits descriptors with copy semantics (and **COW** for memory). **Zombies** appear if the parent never calls `wait()`.

@section: Threads vs processes

**Threads** share the process address space; cheaper context switching but tighter coupling. **Processes** isolate failures but cost more to create IPC.

@section: Scheduling

**Preemptive** multitasking interrupts time slices; **priority inversion** happens if a low-priority thread holds a lock a high-priority thread needs (mitigate with **priority inheritance** on RTOS).

@section: Metrics

**Throughput**, **latency**, **response time**, and CPU **utilization** measure schedulers; no single metric fits all workloads.

@section: Suggested lab

1. On Linux inspect `/proc/<pid>/status` and `ps -o pid,ppid,cmd`.
2. Write a program that forks and measures creation time.
3. Compare `pthread` vs process for a CPU-bound task.

@quiz: What structure does the kernel keep with process metadata?
@option: inode
@correct: PCB / process control block
@option: TLB
