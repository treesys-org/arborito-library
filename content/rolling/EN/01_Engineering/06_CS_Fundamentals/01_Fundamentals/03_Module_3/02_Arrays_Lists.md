@title: Arrays and Linked Lists: Access vs Insertion Trade-offs
@icon: 📚
@description: Contiguous memory vs nodes, doubly linked lists, dynamic vectors.
@order: 2

# Arrays and lists: fundamental linear structures

**Arrays** provide \(O(1)\) index access but \(O(n)\) middle insertion if shifting is required. **Linked lists** insert in \(O(1)\) given a pointer, but random access is \(O(n)\). **Dynamic vectors** amortize resizing by doubling capacity. This lesson compares **cache** friendliness.

@section: Representation

`struct node { T val; node* next; }` vs contiguous `std::vector`.

@section: Variants

**Deque** hybrid; **skip lists** for probabilistic logarithmic search.

@section: Suggested lab

1. Implement a singly linked list with `std::unique_ptr` in C++ or `Box` in Rust.
2. Measure summation time for arrays vs lists for large \(n\).
3. Implement a dynamic vector in C with growth factor 1.5 vs 2.

@quiz: Which operation is typically O(1) for an array at a valid index?
@option: Insert at front
@correct: Index access
@option: Search for an unknown value without ordering
