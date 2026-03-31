@title: Performance Analysis: sar, iostat, vmstat
@icon: 📊
@description: Find bottlenecks in CPU, disk, and memory using the USE method and classic Linux tools.
@order: 1

# Performance engineering: diagnosing bottlenecks

@section: LPIC-2 map — Module 4 (capacity, virtualization, HA, recovery)

Alignment with **LPIC-2** on **capacity, virtualization, and continuity**:

* **200.x / 201.x — Diagnostics:** CPU, memory, I/O, network; `sar`, `iostat`, `vmstat`, `strace`.
* **Virtualization:** KVM/libvirt, guest resource tuning.
* **High availability:** quorum, fencing, Pacemaker (solid intro in the HA lesson).
* **Backup and recovery:** 3-2-1 strategy, tested restores.
* **RHEL:** `tuned` profiles; **any distro:** same **USE** methodology.

“The server is slow” is vague. Your job is to answer **why**: CPU? Disk? Network? Database locks?

This lesson goes beyond staring at `top`. You learn the **USE** method (Utilization, Saturation, Errors), how Linux virtual memory behaves, and how to use forensic tools to find root cause.

@section: 1. USE method (Brendan Gregg)

For each resource (CPU, memory, disk, network), ask:

1. **Utilization** — how busy is it? (e.g. CPU at 90%.)
2. **Saturation** — is work queued because the resource cannot keep up? (high load average, disk wait queue.)
3. **Errors** — physical or logical failures? (dropped packets, I/O errors.)

@section: 2. CPU

**Key concepts:**

* **User time** — your applications (PHP, Java, MySQL). High → application cost.
* **System time** — kernel work (syscalls, drivers). Unusually high → investigate drivers or syscall storms.
* **Nice** — lower-priority processes.
* **Idle** — unused CPU.
* **I/O wait (`wa`)** — **critical:** CPU stalled waiting for **disk**. **High `wa` means the bottleneck is storage, not CPU.**
* **Steal (`st`)** — VMs only: time the hypervisor denied your vCPU.

**`vmstat 1`** — one line per second:

```text
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
```

* **`r`** — runnable processes. If `r` > CPU cores → **CPU saturation**.
* **`b`** — blocked on I/O. High → check disk.

@quiz: `top` shows 100% CPU but 90% is `wa` (I/O wait). What does that mean?
@option: You need a faster CPU
@correct: The CPU is not the problem; storage is slow and the CPU spends most time waiting for I/O
@option: Crypto miner
@option: You need more RAM

@section: 3. Memory: the “free RAM” myth

Linux uses “unused” RAM for **page cache**. **`free -h`** often shows low “free” but high **buff/cache** — that is usually **good**. Watch **available** — memory you can use before swapping.

**Swapping:** disk is orders of magnitude slower than RAM. Persistent **`si`/`so`** activity in `vmstat` means you are in **bad territory**.

**OOM killer:** if RAM and swap are exhausted, the kernel may **kill** the largest process (often a database). Check `dmesg | grep -i "killed process"`.

@section: 4. Disk I/O

**`iostat -xz 1`** (from `sysstat`):

* **r/s, w/s** — IOPS.
* **await** — average wait (ms). High on SSD → latency issue.
* **avgqu-sz** — queue depth; > 1 suggests **saturation**.
* **%util** — time disk was busy; sustained ~100% → disk is the bottleneck.

**`iotop`** — which process is doing the I/O.

@section: 5. Network

**`sar -n DEV 1`** — throughput and packet rates. Tiny packets at huge **rxpck/s** with low throughput can indicate attacks or pathological chatter. Compare to link capacity (1 Gbps ≈ 125 MB/s).

@section: 6. Deep dive: `strace`

**`strace -p PID`** shows live syscalls — where a process **blocks** (`connect` to DB, `read` on NFS). **Warning:** strace slows the target; use briefly.

@section: 7. Tool cheat sheet

| Symptom | Quick | Deeper | Look for |
| :--- | :--- | :--- | :--- |
| General slowness | `top`, `htop` | `vmstat 1` | Load > cores, high `wa` |
| High CPU | `top` | `perf`, `strace` | User vs system, runnable queue |
| Memory | `free -m` | `vmstat` | swap in/out, OOM in logs |
| Slow disk | `iostat -xz 1` | `iotop` | high %util, await, queue |
| Network | `iftop`, `nload` | `sar -n DEV`, `tcpdump` | bandwidth, errors |

Mastering this workflow is what separates a “reboot engineer” from a real systems engineer.

@quiz: Which metric in `vmstat` best indicates memory pressure leading to thrashing?
@option: High `us`
@correct: Non-zero sustained `si` and `so` (swap in/out)
@option: High `id`

@quiz: In `iostat`, which column most directly suggests disk saturation?
@option: r/s only
@correct: High avgqu-sz and await with %util near 100%
@option: rkB/s alone
