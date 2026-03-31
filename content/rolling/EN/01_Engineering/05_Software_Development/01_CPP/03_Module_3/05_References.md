@title: References, Values, Move Semantics
@icon: ↔️
@description: T&&, std::move, perfect forwarding.
@order: 5

# Move semantics (C++11+)

Distinguish lvalues/rvalues; use **`std::move`** to enable move overloads; **`std::forward`** in templates. **Move constructors** transfer internal resources.

@quiz: What does std::move actually do?
@option: Deep copy
@correct: Casts to an xvalue to select move overloads
@option: Frees memory
