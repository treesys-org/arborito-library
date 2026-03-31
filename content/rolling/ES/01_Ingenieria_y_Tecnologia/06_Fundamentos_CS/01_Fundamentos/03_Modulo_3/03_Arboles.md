@title: Árboles binarios: BST, balanceo y recorridos
@icon: 🌿
@description: Inorden, preorden, postorden, AVL/rojo-negro y heaps.
@order: 3

# Árboles: jerarquía y búsqueda

Un **árbol binario** modela jerarquías. **BST** mantiene invariante ordenada; **inorden** visita ordenado. **AVL** y **red-black** garantizan altura \(O(\log n)\). Los **heap** binarios implementan colas de prioridad en arreglo. Esta lección cubre **rotaciones** y **recorridos DFS/BFS**.

@section: Complejidad

Búsqueda en BST balanceado \(O(\log n)\); degenerado en lista \(O(n)\).

@section: Aplicaciones

**Trie** para strings; **segment trees** para consultas de rango.

@section: Laboratorio sugerido

1. Implementa insert/búsqueda BST sin balanceo y genera datos ordenados para ver peor caso.
2. Implementa heap `push/pop` con `std::priority_queue`.
3. Recorre árbol con DFS iterativo usando pila.

@quiz: ¿Qué recorrido de un BST visita las claves en orden ascendente cuando el árbol es válido?
@option: Preorden
@correct: Inorden (in-order)
@option: Postorden únicamente
