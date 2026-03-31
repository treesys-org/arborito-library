@title: Genéricos, borrado de tipos y wildcards PECS
@icon: 🔤
@description: Type erasure, bounded types, ? extends / ? super.
@order: 2

# Genéricos, borrado de tipos y wildcards PECS

Los **genéricos** en Java se **borran** en tiempo de ejecución: `List<String>` y `List<Integer>` comparten la misma clase `List` en bytecode. Eso impide `new T[]` y exige casts con aviso en ciertos casos.

**PECS:** *Producer Extends, Consumer Super*. Si solo **lees** `T` desde una colección, usa `? extends T`; si solo **escribes**, `? super T`.

```java
public static <T> void copy(List<? extends T> src, List<? super T> dst) {
    for (T t : src) dst.add(t);
}
```

@section: Errores frecuentes

* Crear arrays de tipos genéricos.
* Mezclar raw types con genéricos y silenciar *unchecked* warnings sin revisar.
@quiz: En PECS, si una lista solo produce elementos T, ¿qué wildcard usarías?
@option: ? super T
@correct: ? extends T
@option: T sin wildcard
