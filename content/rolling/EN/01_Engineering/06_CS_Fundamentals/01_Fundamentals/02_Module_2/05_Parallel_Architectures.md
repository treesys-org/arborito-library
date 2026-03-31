@title: Parallel Architectures: SIMD, Multithreading, Accelerators
@icon: 🧩
@description: Flynn taxonomy, GPUs, TPUs, programming models.
@order: 5

# Parallelism: from cores to accelerators

**Flynn’s taxonomy**: SISD, SIMD, MISD, MIMD. Modern CPUs combine **multicore MIMD** with **SIMD** vectors. **GPUs** are throughput machines with thousands of lightweight threads. **TPUs/FPGAs** accelerate specific workloads. This lesson contrasts **Amdahl** (serial limit) and **Gustafson** (scale the problem).

@section: Programming models

**OpenMP** (shared memory), **MPI** (distributed), **CUDA**/HIP for GPUs.

@section: Distributed synchronization

**Barriers**, **reductions**, **collectives**; network latency limits strong scaling.

@section: Suggested lab

1. Parallelize a reduction with OpenMP and measure speedup vs threads.
2. Estimate Amdahl’s limit if 40% of the code cannot be parallelized.
3. Run a vector-add tutorial on GPU.

@quiz: Which law limits speedup when a fraction of the program is inherently serial?
@option: Gustafson’s law
@correct: Amdahl’s law
@option: Moore’s law
