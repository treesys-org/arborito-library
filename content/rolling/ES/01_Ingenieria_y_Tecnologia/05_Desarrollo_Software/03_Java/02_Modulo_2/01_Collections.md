@title: Collections Framework: List, Set, Map y complejidades
@icon: 🧺
@description: Interfaces, implementaciones y costes amortizados.
@order: 1

# Collections Framework: List, Set, Map y complejidades

`java.util` ofrece **List** (ordenada, duplicados), **Set** (sin duplicados), **Map** (clave→valor). Elige implementación según **orden** y **coste** esperado.

| Tipo | Implementación típica | Notas |
|------|----------------------|-------|
| List | `ArrayList` | acceso índice O(1), inserción final amortizada O(1) |
| List | `LinkedList` | inserción en medio si ya tienes nodo — peor localidad de caché |
| Set | `HashSet` | basado en `HashMap`, O(1) promedio |
| Set | `TreeSet` | orden total O(log n) |
| Map | `HashMap` | O(1) promedio; requiere `equals/hashCode` correctos |
| Map | `TreeMap` | claves ordenadas O(log n) |

@section: Iteración y ConcurrentModification

Iterar con `Iterator.remove()` es seguro; modificar la colección mientras haces foreach puede lanzar `ConcurrentModificationException`.

@section: Errores frecuentes

* Usar `Vector`/`Hashtable` legacy sin motivo.
* Mutar objetos usados como claves en `HashMap`.
* Construir índices duplicados manualmente cuando `Map` ya modela la relación.
@quiz: ¿Qué Map mantiene las claves ordenadas según orden natural o Comparator?
@option: HashMap
@correct: TreeMap
@option: LinkedHashMap siempre ordenado por valor
