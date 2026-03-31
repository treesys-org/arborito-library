@title: Tipos primitivos en C++: enteros, flotantes y conversiones
@icon: 🔢
@description: int32_t, size_t, overflow, narrowing y literales.
@order: 2

# Tipos primitivos y sus trampas

C++ hereda tipos de C con **ancho dependiente de implementación** salvo `<cstdint>` (`int32_t`, `uint64_t`). **size_t** indexa contenedores. **Conversiones** implícitas y **narrowing** en inicializadores `{}` pueden sorprender. Esta lección conecta **UB** en signed overflow y **NaN** en IEEE-754.

@section: Enteros

Prefiere tipos fijos en APIs públicas; para aritmética mixta, **promoción** entera.

@section: Float/double

Comparaciones exactas son frágiles; usa tolerancias o enteros escalados.

@section: `auto`

Deduce tipos; **no** oscurezcas tipos críticos sin documentar.

@section: Laboratorio

1. Demuestra overflow en `unsigned` vs `signed` (UB).
2. Usa `static_cast` para conversiones explícitas.

@quiz: ¿Qué riesgo tiene el desbordamiento de enteros con signo en C++?
@option: Siempre lanza excepción
@correct: Comportamiento indefinido (undefined behavior)
@option: Se satura en todos los compiladores
