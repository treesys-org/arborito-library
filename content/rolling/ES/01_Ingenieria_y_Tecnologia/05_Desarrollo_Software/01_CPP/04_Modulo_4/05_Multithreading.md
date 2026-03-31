@title: std::thread, mutex y condition_variable
@icon: 🧵
@description: join/detach, data races, memory_order.
@order: 5

# Concurrencia estándar

**std::thread** ejecuta callable; **join** espera. **mutex/lock_guard/unique_lock** protegen datos. **condition_variable** para productor/consumidor. **atomic** para flags simples.

@section: JThread

C++20 join automático en destrucción.

@section: memory_order

Para expertos; empieza con `memory_order_seq_cst`.

@quiz: ¿Qué ocurre si destruyes un std::thread joinable sin join ni detach?
@option: Se cancela
@correct: std::terminate
@option: Se vuelve daemon
