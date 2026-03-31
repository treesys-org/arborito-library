@title: Jerarquía de memoria: localidad, cachés y coherencia
@icon: 📶
@description: L1/L2/L3, líneas, políticas de reemplazo y protocolos MSI/MESI.
@order: 4

# Jerarquía de memoria: rapidez vs tamaño

**Localidad temporal y espacial** justifican **cachés** L1/L2/L3 con líneas (64B típico). **Políticas** de escritura write-back vs write-through. En multiprocesadores, **coherencia** (MESI) mantiene una vista consistente de líneas compartidas. **False sharing** degrada rendimiento si dos hilos escriben líneas distintas en la misma **cache line**.

@section: TLB y página walks

Fallos de TLB disparan **page table walks** costosos.

@section: NUMA

Memoria no uniforme: acceso remoto más lento; **first-touch** policy afecta asignación.

@section: Laboratorio sugerido

1. Usa `perf stat` para contadores `cache-misses`, `L1-dcache-loads`.
2. Microbenchmark con arrays separados vs campos intercalados para ver false sharing.
3. Lee `lscpu` y mapa caches.

@quiz: ¿Qué problema intenta resolver la coherencia de caché en CPUs multinúcleo?
@option: Apagar núcleos
@correct: Mantener consistencia de copias de la misma línea de caché entre núcleos
@option: Aumentar el tamaño de disco
