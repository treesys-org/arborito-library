@title: Generics, type erasure, and PECS
@icon: 🔤
@description: Bounded types, wildcards, limitations.
@order: 2

# Generics, type erasure, and PECS

Java **erases** generic type parameters at runtime. **PECS** guides wildcards: **Producer Extends, Consumer Super**.

```java
public static <T> void copy(List<? extends T> src, List<? super T> dst) {
    for (T t : src) dst.add(t);
}
```

@section: Pitfalls

* Generic array creation.
* Raw types mixing with parameterized types.
@quiz: In PECS, which wildcard fits a producer of T?
@option: ? super T
@correct: ? extends T
@option: T without wildcard
