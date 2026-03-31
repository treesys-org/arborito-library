@title: Instalación de Rust: rustup, toolchains y cargo
@icon: 🦀
@description: rustc, cargo new, rustup default y targets.
@order: 1

# Instalación de Rust: rustup, toolchains y cargo

`rustup` instala **rustc**, **cargo** y **std** para el triple objetivo (host). `rustup toolchain` gestiona versiones **stable/beta/nightly**. `Cargo.toml` declara dependencias y edition.

@section: Comandos básicos
```bash
cargo new hello --bin
cd hello && cargo run
```

@section: Targets
`rustup target add wasm32-unknown-unknown` para WASM.

@quiz: ¿Qué herramienta gestiona toolchains y componentes de Rust?
@option: pip
@correct: rustup
@option: cmake
