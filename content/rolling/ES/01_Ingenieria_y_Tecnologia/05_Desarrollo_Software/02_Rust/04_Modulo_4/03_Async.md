@title: Async Rust: await y executors
@icon: ⚡
@description: Future, Pin, tokio runtime.
@order: 3

# Async Rust: await y executors

Las **futures** son perezosas; requieren **executor** (tokio/async-std). **async fn** retorna impl Future.

@quiz: ¿Qué palabra clave pausa una async function hasta completar otra Future?
@option: yield
@correct: await
@option: suspend
