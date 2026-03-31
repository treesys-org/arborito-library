@title: Contenedores STL: vector, deque, map, unordered_map
@icon: 🧺
@description: Complejidades, invalidación de iteradores, allocators.
@order: 1

# Contenedores estándar

**vector** contiguo amortizado O(1) push_back; **deque** segmentado. **map** árbol ordenado O(log n); **unordered_map** hash O(1) promedio. Conoce **invalidación** de iteradores al reasignar/rehash.

@section: Elegir

Orden vs hash; memoria vs CPU.

@section: Allocator

Personaliza memoria para pools o arenas.

@quiz: ¿Qué operación puede invalidar todos los iteradores de un vector?
@option: push_back siempre
@correct: reallocate si capacity se agota (p. ej. push_back que crece)
@option: at() con índice válido
