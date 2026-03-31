@title: Funciones: fn, expresiones y divergencia
@icon: ⚙️
@description: Última expresión sin ;, never type, impl Trait.
@order: 3

# Funciones: fn, expresiones y divergencia

Las funciones devuelven la última expresión si no termina en `;`. `-> !` indica **divergencia**. `impl Trait` en retorno para futuros/iteradores opacos.

@quiz: ¿Qué hace un `;` al final del cuerpo de una función con tipo de retorno no unit?
@option: Nada
@correct: Convierte la expresión en statement y suele exigir `()` o error de tipos
@option: Optimiza el binario
