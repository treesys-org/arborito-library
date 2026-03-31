@title: Aritmética de punteros y arrays
@icon: ➕
@description: Incremento en arrays, subíndices, decay y UB fuera de rango.
@order: 2

# Aritmética de punteros

Para tipo `T`, `p+1` avanza `sizeof(T)` bytes. **Arrays** y punteros están ligados: `a[i]` es `*(a+i)`. Fuera de rango → **UB**. Esta lección conecta **iteradores** con punteros en contiguos.

@section: Diferencia

`p-q` da número de elementos si mismo tipo completo.

@section: void*

No aritmética directa hasta cast a `char*`.

@quiz: ¿Qué ocurre al desreferenciar más allá del array?
@option: Siempre lanza std::out_of_range
@correct: Comportamiento indefinido
@option: Devuelve 0
