@title: Pointer Arithmetic and Arrays
@icon: ➕
@description: Incrementing in arrays, decay, out-of-range UB.
@order: 2

# Pointer arithmetic

For type `T`, `p+1` advances by `sizeof(T)` bytes. `a[i]` is `*(a+i)`. Out-of-range access is **UB**.

@quiz: What happens when dereferencing past the end of an array?
@option: std::out_of_range
@correct: Undefined behavior
@option: Returns 0
