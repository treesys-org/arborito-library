@title: Arrays C y std::string / string_view
@icon: 📝
@description: buffers, terminación nula, SSO y vistas sin copia.
@order: 4

# Arrays y cadenas: seguridad y ergonomía

Los **arrays C** `T[N]` son tamaño fijo; los **decay** a punteros en parámetros. **`std::string`** gestiona memoria y **SSO** (small string optimization). **`std::string_view`** (C++17) evita copias en parámetros de solo lectura pero **no** propietario.

@section: APIs seguras

Prefiere `std::array` para tamaño fijo con `.size()`. Evita `strcpy` sin límites.

@section: UTF-8

`std::string` almacena bytes; **Unicode** requiere bibliotecas o procesamiento consciente.

@section: Laboratorio

1. Pasa `string_view` a una función que parsea tokens.
2. Mide copias evitadas vs `const std::string&` en microbenchmark.

@quiz: ¿Qué riesgo tiene mantener un `string_view` apuntando a un `string` temporal?
@option: Ninguno
@correct: Dangling reference: el buffer puede desaparecer al terminar la expresión
@option: Siempre duplica la cadena
