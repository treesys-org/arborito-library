@title: <algorithm> y rangos C++20
@icon: 🔍
@description: sort, lower_bound, ranges::views, complejidades.
@order: 2

# Algoritmos genéricos

**std::sort** introsort O(n log n). **lower_bound** en rangos ordenados. **ranges** (`views::filter`) componen transformaciones perezosas. Siempre **valida precondiciones** (ordenación).

@section: Ejecución paralela

**execution::par_unseq** (políticas de ejecución).

@quiz: ¿Qué requisito tiene lower_bound sobre el rango?
@option: Estar vacío
@correct: Estar ordenado según el criterio de comparación
@option: Tener elementos únicos
