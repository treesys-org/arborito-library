@title: new/delete vs make_unique/make_shared
@icon: 🧱
@description: RAII, exceptions, arrays, rule of zero/five.
@order: 3

# Dynamic memory and RAII

Prefer **smart pointers** and containers over raw `new/delete`. **`std::make_unique`** avoids leaks on exceptions. **RAII** ties resource release to destructors.

@section: Arrays

`new T[n]` requires `delete[]`.

@quiz: Which helper idiomatically creates a unique_ptr?
@option: allocate_shared
@correct: make_unique
@option: enable_shared_from_this
