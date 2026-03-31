@title: Lambdas y capturas
@icon: 🎭
@description: [=,&], mutable, tipos de retorno deducidos, constexpr lambdas.
@order: 4

# Lambdas en C++

**[captures](params) -> ret { body }**. Captura por valor copia; por referencia observa lifetime. **`mutable`** permite mutar copias capturadas. **constexpr** lambdas en C++17+.

@section: Generadores

Combina con `std::function` para callbacks.

@quiz: ¿Qué riesgo hay capturando referencia a local por [&] en lambda que sobrevive a la función?
@option: Ninguno
@correct: Dangling reference
@option: Copia profunda automática
