@title: Option y Result: flujo sin excepciones
@icon: ✅
@description: ?, map, and_then, expect vs unwrap.
@order: 3

# Option y Result: flujo sin excepciones

El operador **`?`** propaga errores en funciones que retornan `Result`. Evita `unwrap` en producción.

@quiz: ¿Qué hace el operador ? en Result?
@option: Ignora errores
@correct: Devuelve temprano el Err al caller
@option: Convierte a panic
