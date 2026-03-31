@title: Stream API and collectors
@icon: 🌊
@description: Lazy pipelines, terminal ops, parallel caution.
@order: 3

# Stream API and collectors

Streams compose **lazy** intermediate ops and a single **terminal** operation that executes the pipeline.

```java
long count = lines.stream()
    .filter(s -> !s.isBlank())
    .map(String::trim)
    .distinct()
    .count();
```

@section: parallelStream

May help CPU-bound work; can hurt with small data, contention, or ordering surprises.

@section: Pitfalls

* Side effects in non-idempotent stream ops.
* Misunderstanding encounter order in parallel streams.
@quiz: What kind of operation actually executes a stream pipeline?
@option: peek
@correct: Terminal operation
@option: filter
