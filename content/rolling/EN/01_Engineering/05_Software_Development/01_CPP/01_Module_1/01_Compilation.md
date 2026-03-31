@title: C++ Compilation: Toolchain, TUs, and Linking
@icon: ⚙️
@description: gcc/clang, -std=c++20, .o objects, ODR, headers, include guards.
@order: 1

# From source to binary: the C++ pipeline

**C++** is usually built with **GCC** or **Clang** in stages: **preprocessing** (`#include`, macros), **compilation** per **translation unit** (`.cpp` → `.o`), **linking** that resolves symbols into an executable or library. This lesson fixes **ODR** (One Definition Rule), **inline**, and header hygiene.

@section: Typical command

```bash
clang++ -std=c++20 -O2 -Wall -Wextra -c main.cpp -o main.o
clang++ main.o -o app
```

`-c` compiles only; without it, compile+link. Consider `-fuse-ld=lld` for a fast linker.

@section: Headers

Declare interfaces; use **include guards** or `#pragma once`. Do not place non-inline function definitions (or globals with ODR issues) in headers across multiple TUs without `inline` / `inline constexpr`.

@section: Libraries

**Static** (`.a`) links into the binary; **dynamic** (`.so`) loads at runtime.

@section: Common mistakes

* Wrong `-l` order (dependencies to the right).
* Template definitions only in `.cpp` without explicit instantiation.

@section: Lab

1. Split a program into `math.h` / `math.cpp` / `main.cpp` and compile in stages.
2. Inspect symbols with `nm -C main.o`.

@quiz: Which stage produces `.o` object files?
@option: Only the linker
@correct: Compilation of each translation unit
@option: Only the preprocessor
