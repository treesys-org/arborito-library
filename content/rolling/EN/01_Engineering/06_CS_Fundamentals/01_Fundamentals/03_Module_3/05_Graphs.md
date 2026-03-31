@title: Graphs: Representations and Basic Traversals
@icon: 🕸️
@description: Adjacency matrix vs list, BFS/DFS, DAGs, shortest paths intro.
@order: 5

# Graphs: arbitrary relationships

A graph \(G=(V,E)\) models networks, dependencies, maps. **Adjacency lists** use \(O(V+E)\) space; **matrices** \(O(V^2)\) for dense graphs or small DP on cliques. **BFS** finds distances in unweighted graphs; **DFS** topologically sorts **DAGs**. This lesson introduces **Dijkstra** (non-negative weights) and mentions **Bellman-Ford**.

@section: Connectivity

**Strongly connected components** (Kosaraju/Tarjan).

@section: Suggested lab

1. Represent a small graph and run BFS from a node.
2. Detect a cycle with colored DFS.
3. Implement Dijkstra with `priority_queue` in C++.

@quiz: Which traversal typically uses a queue to visit layer by layer?
@option: DFS
@correct: BFS
@option: Quicksort
