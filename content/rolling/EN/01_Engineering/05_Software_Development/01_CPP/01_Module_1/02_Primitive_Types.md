@title: Primitive Types: Integers, Floats, Conversions
@icon: 🔢
@description: int32_t, size_t, overflow, narrowing, literals.
@order: 2

# Primitive types and their traps

C++ inherits C types with **implementation-defined widths** unless you use `<cstdint>` (`int32_t`, `uint64_t`). **size_t** indexes containers. Implicit **conversions** and **narrowing** in `{}` initialization can surprise. This lesson connects **UB** on signed overflow and **NaN** in IEEE-754.

@section: Integers

Prefer fixed-width types in public APIs; watch integer **promotion** rules.

@section: Float/double

Exact comparisons are fragile; use tolerances or scaled integers.

@section: `auto`

Deduces types; don’t hide safety-critical types without documentation.

@quiz: What is the risk of signed integer overflow in C++?
@option: Always throws
@correct: Undefined behavior
@option: Saturates on all compilers
