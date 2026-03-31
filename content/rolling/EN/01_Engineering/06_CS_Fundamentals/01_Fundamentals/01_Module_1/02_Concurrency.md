@title: Concurrency: Mutual Exclusion, Deadlocks, and Models
@icon: 🔀
@description: Locks, semaphores, races, and memory ordering.
@order: 2

# Concurrency: safety under interleaving

**Concurrency** enables simultaneous progress (multicore) or interleaving (single core). **Mutexes** protect **critical sections**; **semaphores** count resources; **condition variables** synchronize on predicates. This lesson covers **race conditions**, **deadlock** (circular wait), **livelock**, and **memory ordering** in modern languages.

@section: Mutex and RAII

In C++ `std::mutex` + `std::lock_guard`; in Rust the borrow checker prevents data races at compile time. **Lock fewer** mutexes and in a fixed order to reduce deadlocks.

@section: Memory models

**Happens-before** defines visibility of writes across threads. **Acquire/release** fences in C11/C++11; **volatile** is not enough for synchronization.

@section: Patterns

**Reader-writer locks** for read-heavy workloads; **lock-free** structures only with proofs or mature libraries.

@section: Common mistakes

* Double-locking the same mutex.
* **TOCTOU** checking then acting without a lock.

@section: Suggested lab

1. Intentionally create a data race in C and observe nondeterministic results.
2. Fix with a mutex and measure overhead.
3. Implement a bounded producer-consumer queue with a condition variable.

@quiz: What condition typically defines deadlock between threads?
@option: Using queues
@correct: Circular wait where each thread holds one lock and waits for another
@option: High CPU utilization
