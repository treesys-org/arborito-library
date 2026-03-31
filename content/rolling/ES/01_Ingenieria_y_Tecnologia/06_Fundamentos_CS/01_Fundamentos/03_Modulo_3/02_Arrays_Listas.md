@title: Arrays y listas enlazadas: trade-offs de acceso e inserción
@icon: 📚
@description: Memoria contigua vs nodos, listas doblemente enlazadas, vectores dinámicos.
@order: 2

# Arrays y listas: estructuras lineales fundamentales

Los **arrays** ofrecen acceso \(O(1)\) por índice pero inserción central \(O(n)\) si hay que desplazar. Las **listas enlazadas** insertan en \(O(1)\) con puntero previo, pero acceso aleatorio \(O(n)\). Los **vectores dinámicos** amortizan redimensionado duplicando capacidad. Esta lección compara **caché** friendliness.

@section: Representación

`struct node { T val; node* next; }` vs `std::vector` contiguo.

@section: Variantes

**Deque** híbrido; **skip lists** probabilísticas para búsqueda logarítmica.

@section: Laboratorio sugerido

1. Implementa lista simple con `unique_ptr` en C++ o `Box` en Rust.
2. Mide tiempo de suma de arrays vs lista para \(n\) grande.
3. Implementa vector dinámico en C con factor de crecimiento 1.5 vs 2.

@quiz: ¿Qué operación es típicamente O(1) en un array por índice válido?
@option: Insertar al inicio
@correct: Acceso por índice
@option: Buscar un valor desconocido sin orden
