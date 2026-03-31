@title: Programación dinámica: subproblemas y memoización
@icon: 🧩
@description: Top-down vs bottom-up, optimal substructure, LCS y mochila.
@order: 3

# Programación dinámica: evitar recomputar

**DP** aplica cuando hay **subproblemas superpuestos** y **subestructura óptima**. **Memoización** top-down; **tabulación** bottom-up. Ejemplos: **Fibonacci**, **LCS**, **knapsack 0/1**. Esta lección enseña a identificar **estados** y **transiciones**.

@section: Complejidad

Típicamente \(O(\#\text{estados} \times \text{transición})\).

@section: Optimización espacial

A veces solo necesitas dos filas de la tabla.

@section: Laboratorio sugerido

1. Implementa LCS con DP y compara con recursión sin memo.
2. Resuelve knapsack 0/1 para n≤30 con bitmask y DP.
3. Deriva recurrencia para número de caminos en grid con obstáculos.

@quiz: ¿Qué condición hace útil la DP frente a divide y vencerás simple?
@option: Subproblemas independientes sin solapamiento
@correct: Subproblemas superpuestos que se pueden memoizar
@option: Siempre que el input sea aleatorio
