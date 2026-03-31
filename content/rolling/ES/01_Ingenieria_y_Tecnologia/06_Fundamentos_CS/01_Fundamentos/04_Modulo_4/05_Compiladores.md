@title: Compiladores: lexing, parsing y IR
@icon: 🧱
@description: Tokens, AST, análisis semántico, optimización y codegen.
@order: 5

# Pipeline de compilación: del texto al binario

Un **compilador** lee texto fuente, **tokeniza** (regex), **parsea** (LL/LR), construye **AST**, realiza **análisis semántico** (tipos), **optimiza** (SSA, constant folding) y **genera código** (instrucciones máquina). Esta lección resume **front-end** vs **back-end** y **JIT** vs **AOT**.

@section: Análisis

**Lexical** → **syntax** → **semantic**. Errores en cada fase.

@section: Optimizaciones

**Inlining**, **dead code elimination**, **register allocation** (graph coloring).

@section: Enlaces

**Linker** resuelve símbolos; **loader** reubica en RAM.

@section: Laboratorio sugerido

1. Escribe lexer de calculadora con `flex` o a mano.
2. Genera AST simple para `expr + expr`.
3. Inspecciona `clang -O0` vs `-O3` en tamaño de binario.

@quiz: ¿Qué estructura representa típicamente la sintaxis abstracta del programa?
@option: Tabla de símbolos
@correct: AST (árbol de sintaxis abstracta)
@option: Bitmap de disco
