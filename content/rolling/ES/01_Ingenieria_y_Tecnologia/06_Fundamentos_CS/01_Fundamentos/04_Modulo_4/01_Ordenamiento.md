@title: Ordenamiento: mergesort, quicksort y límites
@icon: 🔃
@description: Estabilidad, comparaciones Ω(n log n), counting sort y radix.
@order: 1

# Algoritmos de ordenamiento: teoría y práctica

**Comparación** general tiene **límite Ω(n log n)** en peor caso. **MergeSort** \(O(n\log n)\) estable pero \(O(n)\) extra; **QuickSort** \(O(n\log n)\) promedio, \(O(n^2)\) peor si pivote malo. **Counting/Radix** rompen el límite cuando las claves son enteros acotados. Esta lección cubre **estabilidad** y **in-place**.

@section: HeapSort

\(O(n\log n)\) in-place, no estable.

@section: std::sort

Introsort híbrido en libstdc++.

@section: Laboratorio sugerido

1. Implementa merge sort y mide vs `qsort`.
2. Construye caso adversarial para quicksort Lomuto.
3. Ordena enteros grandes con radix base 256.

@quiz: ¿Qué propiedad conserva un orden estable ante claves iguales?
@option: Velocidad del reloj
@correct: El orden relativo original entre elementos con claves iguales
@option: Uso de memoria cero
