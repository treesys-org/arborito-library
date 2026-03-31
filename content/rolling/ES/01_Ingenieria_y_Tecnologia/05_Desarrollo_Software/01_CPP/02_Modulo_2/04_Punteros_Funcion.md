@title: Punteros a función y std::function
@icon: 📞
@description: Callbacks, plantillas, coste de type erasure.
@order: 4

# Funciones de orden superior en C++

Los **punteros a función** `int(*)(int)` sirven para callbacks C. **`std::function`** envuelve cualquier invocable con coste de **type erasure**. Para hot paths, plantillas y **`auto`** evitan overhead.

@section: std::bind

Legado; prefiere lambdas.

@section: Lambdas

`[captures](params){}`; captura por valor/referencia con cuidado de **lifetime**.

@quiz: ¿Qué construcción suele ser más legible que std::bind para partial application?
@option: Macros
@correct: Lambdas
@option: goto
