@title: I/O: Block vs Character, Drivers, and DMA
@icon: 🔌
@description: read/write syscalls, disk schedulers, NVMe, latency.
@order: 5

# Input/output: from syscall to wire

**I/O** connects CPUs to disks, networks, and peripherals. **Block devices** (512B–4KiB sectors) vs **character** streams. **DMA** moves data to buffers without per-byte CPU work. This lesson summarizes **schedulers** (mq-deadline, bfq), **NVMe** parallel queues, and **epoll/select** for sockets.

@section: Syscalls

`read`/`write` copy between user and kernel space; **zero-copy** (`sendfile`) avoids extra copies.

@section: Disk

**NCQ** reorders requests; **SSDs** change the game (low latency, write wear). **TRIM** informs free blocks.

@section: Networking

Socket **buffers** and **backpressure**; **NAPI** on Linux for network interrupts.

@section: Common mistakes

* Massive synchronous writes without batching.
* Leaking file descriptors without `close`.

@section: Suggested lab

1. Measure IOPS with `fio` random vs sequential.
2. Watch `iostat -x 1` under load.
3. Write a minimal `epoll` server and compare to one-thread-per-connection.

@quiz: What mechanism lets devices transfer memory without CPU involvement for every byte?
@option: IRQ only
@correct: DMA (direct memory access)
@option: Mutex
