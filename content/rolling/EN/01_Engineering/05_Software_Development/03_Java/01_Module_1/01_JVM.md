@title: JVM, bytecode, and garbage collection
@icon: ☕
@description: HotSpot, JIT, heap, metaspace, GC pauses.
@order: 1

# JVM, bytecode, and garbage collection

Compiling with `javac` does not emit native machine code for your CPU directly: it emits **bytecode** stored in `.class` files. The **Java Virtual Machine (JVM)** is the runtime that executes that bytecode on Linux, Windows, or macOS without rebuilding the whole project per OS.

This lesson anchors **JDK vs JRE vs JVM**, how classes load, where objects live, and why applications sometimes pause briefly (**GC pauses**).

@section: JDK, JRE, and JVM

* **JDK:** includes `javac`, diagnostic tools (`jcmd`, `jmap`, JFR), and usually a bundled runtime.
* **JRE:** JVM + standard libraries to **run** (not necessarily to compile).
* **JVM:** interpreter + JIT compilers + memory/GC subsystem.

@section: Bytecode and .class files

Bytecode is **stack-based**. Inspect it with:

```bash
javap -c -p my/pkg/MyClass
```

Each method becomes a verified instruction sequence (**bytecode verifier**).

@section: Class loading

Classes load **on demand**. ClassLoaders resolve the **classpath** or **module-path** (Java 9+). Common failures:

* `ClassNotFoundException` — `.class` missing at runtime.
* `NoClassDefFoundError` — compiled fine, failed to load later (missing dependency).

@section: Memory: heap, metaspace, stacks

* **Heap:** Java objects.
* **Metaspace:** class metadata (modern replacement for PermGen).
* **Per-thread stack:** frames with locals; not where big objects live.

@section: JIT compilation

The JVM starts interpreting. **Hot methods** get JIT-compiled (HotSpot **C1** vs **C2**). Benchmarks must **warm up** before measuring.

@section: Collectors (G1, ZGC, Shenandoah)

**G1** regions target bounded pauses. **ZGC** / **Shenandoah** aim for very low pause times on large heaps with CPU trade-offs.

@section: JPMS modules

`module-info.java` exports packages and requires modules—structuring large apps beyond a flat classpath.

@section: Lab

1. Compile `Hello.java`, inspect `.class`.
2. Run with GC logging and allocate temporary objects in a loop.
3. `jcmd <pid> VM.flags` for effective flags.

@section: Common pitfalls

* Benchmarking without JIT warmup.
* Race conditions around nullability and visibility across threads.
* Tuning GC without metrics.
@quiz: Which component compiles hot bytecode methods to native code?
@option: Static linker only
@correct: JIT compiler
@option: javac on every run
