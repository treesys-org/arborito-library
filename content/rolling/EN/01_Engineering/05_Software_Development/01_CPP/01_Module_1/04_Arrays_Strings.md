@title: C Arrays and std::string / string_view
@icon: 📝
@description: Buffers, null termination, SSO, non-owning views.
@order: 4

# Arrays and strings: safety and ergonomics

**C arrays** `T[N]` are fixed size; they **decay** to pointers in parameters. **`std::string`** manages memory and may use **SSO**. **`std::string_view`** (C++17) avoids copies for read-only parameters but **does not own** storage.

@section: Safer APIs

Prefer `std::array` for fixed size with `.size()`. Avoid unbounded `strcpy`.

@section: UTF-8

`std::string` stores bytes; Unicode needs conscious handling.

@quiz: What is risky about keeping a `string_view` to a temporary `string`?
@option: No risk
@correct: Dangling view after the temporary is destroyed
@option: Always duplicates the string
