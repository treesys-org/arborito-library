@title: Collections Framework
@icon: 🧺
@description: Lists, sets, maps, and amortized costs.
@order: 1

# Collections Framework

`java.util` provides **List**, **Set**, and **Map**. Pick implementations for **ordering** and **performance** characteristics.

* **`ArrayList`** — fast random access; good default list.
* **`HashMap`** — average O(1); keys need consistent `equals/hashCode`.
* **`TreeMap` / `TreeSet`** — sorted order, O(log n).

@section: Safe iteration

Use `Iterator.remove()` for safe removal while iterating; external structural changes during enhanced for can throw `ConcurrentModificationException`.

@section: Pitfalls

* Mutable keys inside `HashMap`.
* Accidentally using legacy synchronized collections without need.
@quiz: Which map keeps keys sorted (natural or Comparator order)?
@option: HashMap
@correct: TreeMap
@option: LinkedHashMap by value order
