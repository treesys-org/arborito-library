@title: Stream API: operaciones intermedias y terminales
@icon: 🌊
@description: map/filter/reduce, collectors, parallelStream con cautela.
@order: 3

# Stream API: operaciones intermedias y terminales

Un **Stream** es una secuencia potencialmente paralela con **operaciones intermedias** (lazy) y **una terminal** que dispara el cómputo.

```java
long count = lines.stream()
    .filter(s -> !s.isBlank())
    .map(String::trim)
    .distinct()
    .count();
```

**Collectors** agrupan, particionan y acumulan. `groupingBy` + `mapping` es un patrón habitual.

@section: parallelStream

Puede acelerar CPU-bound puros, pero empeora si hay pocos elementos, contención o orden no determinista.

@section: Errores frecuentes

* Efectos secundarios dentro de `map`/`forEach` no idempotentes.
* Asumir que `findFirst` en paralelo es “el primero del origen” sin orden definido.
@quiz: ¿Qué tipo de operación dispara realmente el pipeline de un Stream?
@option: peek
@correct: Operación terminal
@option: filter
