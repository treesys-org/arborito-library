@title: Flujo de control: condicionales, bucles y rangos
@icon: 🔁
@description: if constexpr, switch, rangos for-each, break/continue.
@order: 3

# Control de flujo moderno

C++ ofrece `if`, `switch`, `while`, `for` clásico y **range-based for** sobre contenedores. **if constexpr** (C++17) evalúa ramas en **compile-time** para plantillas. **[[fallthrough]]** documenta casos en `switch`. Evita **macros** para lógica.

@section: switch

Mejor con **enumeraciones** `enum class` fuertemente tipadas.

@section: Bucles

Prefiere algoritmos STL (`std::for_each`, `ranges`) cuando clarifica intención.

@section: Laboratorio

1. Reescribe un `for` indexado por range-for con `std::vector`.
2. Usa `if constexpr` para seleccionar implementación según tipo.

@quiz: ¿Qué característica distingue `if constexpr` de `if` normal en plantillas?
@option: Solo funciona en runtime
@correct: Descarta ramas no seleccionadas en tiempo de compilación
@option: Siempre evalúa ambas ramas
