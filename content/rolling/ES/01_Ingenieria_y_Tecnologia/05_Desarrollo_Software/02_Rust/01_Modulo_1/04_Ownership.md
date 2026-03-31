@title: Ownership: reglas únicas
@icon: 🧩
@description: Movimiento, Copy, Drop y RAII.
@order: 4

# Ownership: reglas únicas

Cada valor tiene un único dueño; al salir de alcance se llama **drop**. **Move** transfiere ownership. Tipos `Copy` se copian bit a bit.

@quiz: ¿Qué ocurre al asignar un String a otra variable sin clonar?
@option: Se copia automáticamente
@correct: Move: el original queda invalidado
@option: Se compila solo en nightly
