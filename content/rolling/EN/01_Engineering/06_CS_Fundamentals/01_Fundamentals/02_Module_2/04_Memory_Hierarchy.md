@title: Memory Hierarchy: Locality, Caches, and Coherence
@icon: 📶
@description: L1/L2/L3, cache lines, replacement, MSI/MESI protocols.
@order: 4

# Memory hierarchy: speed vs size

**Temporal and spatial locality** justify **L1/L2/L3 caches** with **lines** (64B typical). **Write-back** vs **write-through** policies. On multiprocessors, **coherence** (MESI) keeps a consistent view of shared lines. **False sharing** hurts performance when two threads write different fields on the same **cache line**.

@section: TLB and page walks

TLB misses trigger costly **page table walks**.

@section: NUMA

Non-uniform memory access: remote memory is slower; **first-touch** policy affects allocation.

@section: Suggested lab

1. Use `perf stat` for `cache-misses`, `L1-dcache-loads`.
2. Microbenchmark separate arrays vs interleaved fields to see false sharing.
3. Read `lscpu` and map caches.

@quiz: What problem does cache coherence solve on multicore CPUs?
@option: Turning off cores
@correct: Keeping consistent copies of the same cache line across cores
@option: Increasing disk size
