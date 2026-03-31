@title: Exceptions: checked, unchecked, try-with-resources
@icon: ⚠️
@description: Throwable hierarchy, AutoCloseable, suppressed exceptions.
@order: 5

# Exceptions: checked, unchecked, try-with-resources

`Throwable` splits into **Error** (usually not caught) and **Exception**. **Checked** exceptions must be declared or handled; **RuntimeException** types are **unchecked**.

@section: try-with-resources

```java
try (var in = Files.newInputStream(path)) {
    // use stream
}
```

@section: Suppressed exceptions

Secondary failures during `close()` attach as **suppressed** to the primary throwable.

@section: Practices

* Never swallow exceptions silently.
* Prefer specific exception types in public APIs.
* Preserve cause chains in async stacks.
@quiz: Which interface enables try-with-resources?
@option: Serializable
@correct: AutoCloseable
@option: Flushable
