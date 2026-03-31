@title: STL Containers: vector, deque, map, unordered_map
@icon: 🧺
@description: Complexities, iterator invalidation, allocators.
@order: 1

# Standard containers

**vector** is contiguous; **deque** is segmented. **map** is ordered O(log n); **unordered_map** hashes O(1) average. Know **iterator invalidation** on reallocation/rehash.

@quiz: Which vector operation may invalidate iterators when growth happens?
@option: at() valid index
@correct: push_back that triggers reallocation
@option: size()
