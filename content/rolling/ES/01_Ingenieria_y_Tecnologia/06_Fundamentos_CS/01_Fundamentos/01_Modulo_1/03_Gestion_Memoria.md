@title: Gestión de memoria: paginación, TLB y swapping
@icon: 💾
@description: Espacios virtuales, page faults, algoritmos de reemplazo y fragmentación.
@order: 3

# Gestión de memoria: virtual, rápida y a prueba de fragmentación externa

Los SO modernos usan **memoria virtual** con **paginación**: tablas de páginas multi-nivel, **TLB** para acelerar traducción, y **page faults** para **demand paging** y **copy-on-write**. Esta lección resume **reemplazo** (LRU aproximado, reloj), **thrashing** y **overcommit**.

@section: Paginación

Direcciones virtuales → físicas vía **page table entries** con bits de presencia, R/W, NX. **Huge pages** reducen presión de TLB.

@section: Algoritmos de reemplazo

**FIFO**, **LRU** (costoso), **clock** aproximado. El objetivo es minimizar **page faults** según patrón de acceso.

@section: Swapping

Área de **swap** en disco cuando RAM insuficiente; latencias enormes. **OOM killer** en Linux elige procesos víctimas.

@section: Segmentación vs paginación

La **segmentación** histórica sufre fragmentación externa; la paginación la mitiga con **fragmentación interna** pequeña.

@section: Laboratorio sugerido

1. Observa `vmstat`, `sar -B` bajo carga sintética.
2. Limita RAM de un proceso con `ulimit` o cgroups y mide swaps.
3. Calcula overhead de tablas de páginas para un espacio de 48 bits.

@quiz: ¿Para qué sirve principalmente la TLB?
@option: Cifrar RAM
@correct: Cachear traducciones de dirección virtual a física para acelerar accesos
@option: Almacenar el código fuente
