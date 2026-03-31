@title: Concurrencia: exclusion mutua, deadlocks y modelos
@icon: 🔀
@description: Locks, semáforos, condiciones de carrera y memoria de ordenación.
@order: 2

# Concurrencia: seguridad ante ejecución intercalada

La **concurrencia** permite progreso simultáneo (multicore) o intercalado (un core). Los **locks** (`mutex`) protegen **secciones críticas**; los **semáforos** cuentan recursos; las **variables de condición** sincronizan con predicados. Esta lección cubre **condiciones de carrera**, **deadlock** (espera circular), **livelock** y **memory ordering** en lenguajes modernos.

@section: Mutex y RAII

En C++ `std::mutex` + `std::lock_guard`; en Rust el borrow checker evita data races en compile time. **Abrir menos locks** y en orden fijo reduce deadlocks.

@section: Modelos de memoria

**Happens-before** define visibilidad de escrituras entre hilos. **Acquire/release** fences en C11/C++11; **volatile** no es suficiente para sincronización.

@section: Patrones

**Reader-writer locks** para lecturas frecuentes; **lock-free** estructuras solo con pruebas formales o bibliotecas maduras.

@section: Errores frecuentes

* Doble lock del mismo mutex.
* **TOCTOU** al chequear y luego actuar sin lock.

@section: Laboratorio sugerido

1. Provoca data race intencional en C y observa resultados no deterministas.
2. Corrige con mutex y mide overhead.
3. Implementa productor-consumidor con cola acotada y condvar.

@quiz: ¿Qué condición define típicamente un deadlock entre hilos?
@option: Uso de colas
@correct: Espera circular de recursos donde cada hilo retiene un lock y espera otro
@option: Alta utilización de CPU
