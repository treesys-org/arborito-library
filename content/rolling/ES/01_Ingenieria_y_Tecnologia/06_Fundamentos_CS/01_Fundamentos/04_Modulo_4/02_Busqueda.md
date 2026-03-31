@title: Búsqueda: binaria, interpolación y en strings
@icon: 🔍
@description: Lower bound, extremos, y patrones KMP/rolling hash introductorios.
@order: 2

# Búsqueda: reducir espacio de respuesta

**Búsqueda binaria** sobre arreglo ordenado en \(O(\log n)\) comparaciones; **lower_bound** encuentra primer elemento ≥ valor. Generaliza a **búsqueda en respuesta** (monotonía). **KMP** para patrones en texto; **rolling hash** para substrings. Esta lección conecta **ternary search** unimodal.

@section: Errores

**Off-by-one** en índices; bucles infinitos si `mid` mal calculado.

@section: Laboratorio sugerido

1. Implementa lower_bound manual.
2. Resuelve problema de "mínimo tiempo para completar K trabajos" con BS sobre respuesta.
3. Implementa KMP para patrón fijo.

@quiz: ¿Qué requisito esencial para aplicar búsqueda binaria clásica sobre un arreglo?
@option: Que sea de longitud par
@correct: Estar ordenado (según el criterio de comparación)
@option: Que los elementos sean únicos
