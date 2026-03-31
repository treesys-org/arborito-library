@title: Algoritmos voraces: elección local óptima
@icon: 🍯
@description: Propiedad greedy, interval scheduling, mochila fraccionaria.
@order: 4

# Voraces: rápido cuando la prueba de intercambio funciona

Un **algoritmo greedy** elige la opción localmente mejor en cada paso. Solo es correcto si existe **subestructura greedy** y **propiedad de elección greedy** (prueba de intercambio). Ejemplos: **interval scheduling**, **mochila fraccionaria**, **Huffman** (con estructura de datos adecuada). Esta lección contrasta con DP donde greedy falla.

@section: Contraejemplos

**Mochila 0/1** no admite greedy por valor/peso en general.

@section: Laboratorio sugerido

1. Demuestra interval scheduling con finish time.
2. Implementa Huffman para frecuencias dadas.
3. Encuentra caso donde greedy de monedas falla si denominaciones no canónicas.

@quiz: ¿Qué se requiere típicamente para demostrar corrección de un greedy?
@option: Que sea O(n log n)
@correct: Argumento de intercambio o propiedad greedy que sustenta la elección local
@option: Que use cola de prioridad
