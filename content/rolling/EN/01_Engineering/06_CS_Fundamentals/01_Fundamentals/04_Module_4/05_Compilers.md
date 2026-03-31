@title: Compilers: Lexing, Parsing, and IR
@icon: 🧱
@description: Tokens, AST, semantic analysis, optimization, codegen.
@order: 5

# Compiler pipeline: from text to binary

A **compiler** reads source text, **tokenizes** (regex), **parses** (LL/LR), builds an **AST**, performs **semantic analysis** (types), **optimizes** (SSA, constant folding), and **generates code** (machine instructions). This lesson summarizes **front-end** vs **back-end** and **JIT** vs **AOT**.

@section: Analysis phases

**Lexical** → **syntax** → **semantic**. Errors at each stage.

@section: Optimizations

**Inlining**, **dead code elimination**, **register allocation** (graph coloring).

@section: Linking

The **linker** resolves symbols; the **loader** relocates in memory.

@section: Suggested lab

1. Write a tiny calculator lexer with `flex` or by hand.
2. Build a simple AST for `expr + expr`.
3. Compare `clang -O0` vs `-O3` binary sizes.

@quiz: What structure typically represents the program’s abstract syntax?
@option: Symbol table
@correct: AST (abstract syntax tree)
@option: Disk bitmap
