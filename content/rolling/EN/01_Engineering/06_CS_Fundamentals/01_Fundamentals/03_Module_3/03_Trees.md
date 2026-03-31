@title: Binary Trees: BST, Balancing, and Traversals
@icon: 🌿
@description: Inorder/preorder/postorder, AVL/red-black, heaps.
@order: 3

# Trees: hierarchy and search

A **binary tree** models hierarchies. A **BST** maintains a sorted invariant; **inorder** visits sorted. **AVL** and **red-black** trees guarantee \(O(\log n)\) height. **Binary heaps** implement priority queues in arrays. This lesson covers **rotations** and **DFS/BFS** traversals.

@section: Complexity

Balanced BST search \(O(\log n)\); degenerate to a list \(O(n)\).

@section: Applications

**Tries** for strings; **segment trees** for range queries.

@section: Suggested lab

1. Implement BST insert/search without balancing and feed sorted data to see worst case.
2. Implement heap `push/pop` with `std::priority_queue`.
3. Traverse a tree with iterative DFS using a stack.

@quiz: Which BST traversal visits keys in ascending order for a valid BST?
@option: Preorder
@correct: Inorder
@option: Postorder only
