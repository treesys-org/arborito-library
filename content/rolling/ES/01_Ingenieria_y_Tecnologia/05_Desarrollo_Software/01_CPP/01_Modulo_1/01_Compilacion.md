@title: Compilación C++: toolchain, unidades de traducción y enlazado
@icon: ⚙️
@description: gcc/clang, -std=c++20, objetos .o, ODR, headers y #include guards.
@order: 1

# De fuente a binario: el pipeline de C++

**C++** se compila típicamente con **GCC** o **Clang** en varias etapas: **preprocesado** (`#include`, macros), **compilación** por **unidad de traducción** (`.cpp` → `.o`), **enlazado** que resuelve símbolos en un ejecutable o biblioteca. Esta lección fija conceptos **ODR** (One Definition Rule), **inline**, y buenas prácticas de **headers**.

@section: Comando típico

```bash
clang++ -std=c++20 -O2 -Wall -Wextra -c main.cpp -o main.o
clang++ main.o -o app
```

`-c` solo compila; sin él, compila y enlaza. Usa `-fuse-ld=lld` si quieres enlazador rápido.

@section: Headers

Declara interfaces; **include guards** o `#pragma once`. **Nunca** definir funciones no-inline ni variables globales con **ODR** en múltiples TUs sin `inline`/`inline constexpr`.

@section: Bibliotecas

**Estáticas** (`.a`) enlazan en el binario; **dinámicas** (`.so`) cargan en runtime.

@section: Errores frecuentes

* Enlazar orden incorrecto de `-lfoo` (dependencias a la derecha).
* Definir plantillas solo en `.cpp` sin instanciación explícita.

@section: Laboratorio

1. Separa un programa en `math.h`/`math.cpp`/`main.cpp` y compila por etapas.
2. Observa símbolos con `nm -C main.o`.

@quiz: ¿Qué etapa produce archivos objeto `.o`?
@option: Solo el enlazador
@correct: La compilación de cada unidad de traducción
@option: El preprocesador únicamente
