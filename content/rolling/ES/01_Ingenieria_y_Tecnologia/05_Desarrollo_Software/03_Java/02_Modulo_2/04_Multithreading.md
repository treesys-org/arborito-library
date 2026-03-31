@title: Concurrencia: ExecutorService y java.util.concurrent
@icon: 🧵
@description: Pools, Futures, locks y estructuras concurrentes.
@order: 4

# Concurrencia: ExecutorService y java.util.concurrent

Crear un `Thread` por petición no escala. Usa **ExecutorService** con colas acotadas y políticas de rechazo explícitas.

```java
try (var pool = Executors.newFixedThreadPool(8)) {
    Future<Integer> f = pool.submit(() -> heavy());
    int v = f.get();
}
```

**ConcurrentHashMap** ofrece segmentación; no uses `Collections.synchronizedMap` salvo que el acceso sea trivial.

@section: java.util.concurrent

`CountDownLatch`, `Semaphore`, `CompletableFuture` (encadenar etapas async).

@section: Errores frecuentes

* Fugas de hilos por pools no cerrados.
* Deadlocks por orden inconsistente de locks.
* Data races por falta de *happens-before* (volatile, locks, concurrent APIs).
@quiz: ¿Qué interfaz representa típicamente un pool de hilos reutilizable?
@option: ThreadFactory
@correct: ExecutorService
@option: Runnable
