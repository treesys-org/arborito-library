@title: Pattern matching: match y if let
@icon: 🎯
@description: Irrefutable patterns, guards, @ bindings.
@order: 2

# Pattern matching: match y if let

`match` debe ser exhaustivo. `if let` para un solo caso. **Guards** con `if`.

@quiz: ¿Qué error si match no cubre todas las variantes?
@option: Warning solo
@correct: Error de compilación (no exhaustivo)
@option: Runtime panic siempre
