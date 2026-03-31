@title: Arquitecturas paralelas: SIMD, multithread y aceleradores
@icon: 🧩
@description: Flynn taxonomy, GPUs, TPUs y modelos de programación.
@order: 5

# Paralelismo: desde núcleos hasta aceleradores

**Taxonomía de Flynn**: SISD, SIMD, MISD, MIMD. Los CPUs modernos combinan **multicore MIMD** con **SIMD** (vectores). **GPUs** son throughput machines con miles de hilos ligeros. **TPUs/FPGAs** aceleran cargas específicas. Esta lección contrasta **Amdahl** (límite de serial) y **Gustafson** (escala el problema).

@section: Modelos de programación

**OpenMP** (shared memory), **MPI** (distributed), **CUDA**/HIP para GPUs.

@section: Sincronización distribuida

**Barriers**, **reductions**, **collectives**; latencia de red limita strong scaling.

@section: Laboratorio sugerido

1. Paraleliza reducción con OpenMP y mide speedup vs hilos.
2. Estima límite de Amdahl si 40% del código es serializable.
3. Ejecuta ejemplo vector add en GPU tutorial oficial.

@quiz: ¿Qué ley limita la aceleración si una fracción del programa es inherentemente secuencial?
@option: Ley de Gustafson
@correct: Ley de Amdahl
@option: Ley de Moore
