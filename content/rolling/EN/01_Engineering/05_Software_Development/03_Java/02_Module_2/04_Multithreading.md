@title: Concurrency: ExecutorService and j.u.c.
@icon: 🧵
@description: Pools, futures, concurrent collections.
@order: 4

# Concurrency: ExecutorService and j.u.c.

Prefer **ExecutorService** over unbounded thread-per-task patterns.

```java
try (var pool = Executors.newFixedThreadPool(8)) {
    Future<Integer> f = pool.submit(() -> heavy());
    int v = f.get();
}
```

@section: j.u.c.

`ConcurrentHashMap`, `CompletableFuture`, `Semaphore`, `CountDownLatch`.

@section: Pitfalls

* Thread pool leaks.
* Deadlocks from lock ordering.
* Missing *happens-before* edges.
@quiz: Which API typically represents a reusable thread pool?
@option: ThreadFactory
@correct: ExecutorService
@option: Runnable
