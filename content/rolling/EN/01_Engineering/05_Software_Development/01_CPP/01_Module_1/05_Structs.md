@title: struct, union, alignas
@icon: 🧱
@description: Aggregates, POD, padding, alignment.
@order: 5

# Aggregates and memory layout

A **struct** groups members; the compiler inserts **padding** for alignment. **`alignas`** forces alignment. **Designated initializers** (C++20) simplify construction. **union** shares storage—powerful but easy to misuse.

@section: offsetof

Use carefully; layout assumptions can break across compilers/options.

@quiz: Why can two structs with the same members have different sizeof?
@option: C++ forbids it
@correct: Member order and padding change layout
@option: Only because of the struct name
