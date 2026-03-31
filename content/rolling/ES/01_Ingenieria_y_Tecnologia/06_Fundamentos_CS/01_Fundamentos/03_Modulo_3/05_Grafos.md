@title: Grafos: representaciones y recorridos básicos
@icon: 🕸️
@description: Matriz vs lista de adyacencia, BFS/DFS, DAGs y caminos cortos introductorios.
@order: 5

# Grafos: relaciones arbitrarias

Un **grafo** \(G=(V,E)\) modela redes, dependencias, mapas. **Lista de adyacencia** es espacio \(O(V+E)\); **matriz** \(O(V^2)\) útil en densos o DP sobre cliques pequeños. **BFS** halla distancias en no ponderados; **DFS** topológica en **DAG**. Esta lección introduce **Dijkstra** (no negativos) y menciona **Bellman-Ford**.

@section: Conectividad

**Componentes fuertemente conexos** (Kosaraju/Tarjan).

@section: Laboratorio sugerido

1. Representa un grafo pequeño y ejecuta BFS desde un nodo.
2. Detecta ciclo con DFS colores.
3. Implementa Dijkstra con `priority_queue` en C++.

@quiz: ¿Qué recorrido usa típicamente una cola para visitar por capas?
@option: DFS
@correct: BFS
@option: Quicksort
