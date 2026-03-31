@title: Excepciones: checked, unchecked y try-with-resources
@icon: ⚠️
@description: Jerarquía Throwable, try-with-resources, suppressed exceptions.
@order: 5

# Excepciones: checked, unchecked y try-with-resources

`Throwable` ramifica en **Error** (fallos graves, normalmente no capturar) y **Exception**. Las **checked** (`IOException`, …) deben declararse en `throws` o capturarse. Las **RuntimeException** son **unchecked** — no fuerzan la firma del método.

@section: try-with-resources

```java
try (var in = Files.newInputStream(path)) {
    // usa in
}
```

Cualquier `AutoCloseable` se cierra al salir, incluso con excepción.

@section: Suppressed

Si el `close()` lanza mientras ya había otra excepción principal, se adjuntan como **suppressed** — visibles con `getSuppressed()`.

@section: Buenas prácticas

* No tragues excepciones vacías.
* Prefiere excepciones específicas a `Exception` genérica en APIs públicas.
* En APIs reactivas/async, revisa cómo se propagan causas encadenadas (`initCause`).
@quiz: ¿Qué interfaz permite usar un recurso en try-with-resources?
@option: Serializable
@correct: AutoCloseable
@option: Flushable
