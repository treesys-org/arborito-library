@title: Mutex y RwLock en Rust
@icon: 🔒
@description: Poisoning, deadlocks, guardas.
@order: 2

# Mutex y RwLock en Rust

`Mutex::lock` puede **poison** si un hilo panicó sosteniendo el lock.

@quiz: ¿Qué indica unwrap() en Mutex tras poison?
@option: Ignora
@correct: error PoisonError
@option: Reinicia OS
