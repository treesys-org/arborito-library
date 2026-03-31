@title: Referencias, rvalores y move semantics
@icon: ↔️
@description: T&&, std::move, perfect forwarding.
@order: 5

# Semántica de valores en C++11+

**lvalue** vs **rvalue**; **referencias universales** `T&&` con deducción. **`std::move`** es un cast a rvalue; **`std::forward`** preserva valor categoría en plantillas. **Move constructors** roban recursos internos.

@section: Copia vs movimiento

Mejora rendimiento de contenedores grandes.

@section: Regla de cinco extendida

Incluye operadores de movimiento.

@quiz: ¿Qué hace std::move en realidad?
@option: Copia profunda
@correct: Convierte a xvalue (rvalue nombrado) para permitir overload de movimiento
@option: Libera memoria
