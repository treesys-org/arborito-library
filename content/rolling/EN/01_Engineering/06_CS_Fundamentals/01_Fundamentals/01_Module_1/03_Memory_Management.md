@title: Memory Management: Paging, TLB, and Swapping
@icon: 💾
@description: Virtual address spaces, page faults, replacement, fragmentation.
@order: 3

# Memory management: virtual, fast, and resilient to external fragmentation

Modern OSes use **virtual memory** with **paging**: multi-level page tables, a **TLB** to speed translation, and **page faults** for **demand paging** and **copy-on-write**. This lesson summarizes **replacement** (LRU approximations, clock), **thrashing**, and **overcommit**.

@section: Paging

Virtual addresses → physical via **page table entries** with present, R/W, NX bits. **Huge pages** reduce TLB pressure.

@section: Replacement algorithms

**FIFO**, **LRU** (expensive), **clock** approximations. Goal: minimize **page faults** for the access pattern.

@section: Swapping

**Swap** space on disk when RAM is insufficient; huge latency. **OOM killer** on Linux picks victim processes.

@section: Segmentation vs paging

Historical **segmentation** suffers external fragmentation; **paging** trades small **internal** fragmentation.

@section: Suggested lab

1. Watch `vmstat`, `sar -B` under synthetic load.
2. Limit RAM with `ulimit` or cgroups and observe swapping.
3. Compute page table overhead for a 48-bit virtual space.

@quiz: What is the TLB mainly for?
@option: Encrypting RAM
@correct: Caching virtual-to-physical translations to speed accesses
@option: Storing source code
