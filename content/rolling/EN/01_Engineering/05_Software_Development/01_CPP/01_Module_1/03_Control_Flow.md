@title: Control Flow: Conditionals, Loops, Ranges
@icon: 🔁
@description: if constexpr, switch, range-for, break/continue.
@order: 3

# Modern control flow

C++ provides `if`, `switch`, classic `for`, and **range-based for** over containers. **if constexpr** (C++17) evaluates branches at **compile time** in templates. **[[fallthrough]]** documents `switch` cases. Avoid **macros** for control flow.

@section: switch

Prefer strongly typed **`enum class`**.

@section: Loops

Prefer STL algorithms (`std::for_each`, `ranges`) when intent is clearer.

@quiz: What distinguishes `if constexpr` from normal `if` in templates?
@option: Runtime-only
@correct: Discards non-taken branches at compile time
@option: Always evaluates both branches
