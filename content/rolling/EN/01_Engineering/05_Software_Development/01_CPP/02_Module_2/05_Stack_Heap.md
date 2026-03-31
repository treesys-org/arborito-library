@title: Stack vs Heap and Storage Duration
@icon: 🗂️
@description: Automatic, dynamic, static, thread_local.
@order: 5

# Storage duration

Automatic locals live on the **stack** until the block ends. Dynamic allocation lives until freed. **Static** and **thread_local** have extended lifetime.

@quiz: Where do typical function-local `int x` variables live?
@option: Heap
@correct: The function’s stack frame
@option: .bss always
