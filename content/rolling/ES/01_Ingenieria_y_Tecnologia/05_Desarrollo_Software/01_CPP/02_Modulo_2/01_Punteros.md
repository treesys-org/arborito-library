@title: Punteros: direcciones, nullptr y referencias
@icon: 📌
@description: * y &, nullptr_t, punteros a void y conversión explícita.
@order: 1

# Punteros: indirección explícita

Un **puntero** almacena la **dirección** de un objeto. **nullptr** (C++11) representa puntero nulo tipado. Los **references** `T&` son alias obligatorios no nulos. Esta lección distingue **punteros** (rebindable) vs **referencias** y el peligro de **dangling**.

@section: Sintaxis

```cpp
int x = 42;
int* p = &x;
int& r = x;
```

**const T*** vs `T* const` — aprende a leer de derecha a izquierda.

@section: void*

Puntero opaco; convierte con `static_cast` a tipo real.

@section: Errores

* Desreferenciar nullptr (UB).
* Retornar puntero a local de función.

@quiz: ¿Qué literal se prefiere en C++ moderno para puntero nulo?
@option: NULL de C
@correct: nullptr
@option: 0L únicamente
