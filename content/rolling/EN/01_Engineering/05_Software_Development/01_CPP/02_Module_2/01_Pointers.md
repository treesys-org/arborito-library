@title: Pointers: Addresses, nullptr, References
@icon: 📌
@description: * and &, nullptr_t, void*, explicit casts.
@order: 1

# Pointers: explicit indirection

A **pointer** stores an object **address**. **nullptr** represents a null pointer. **References** `T&` are non-null aliases. Learn **const T*** vs `T* const` by reading right-to-left.

@section: void*

Opaque pointer; cast with `static_cast` to real type.

@quiz: Which literal is preferred in modern C++ for null pointers?
@option: NULL
@correct: nullptr
@option: 0 only
