@title: Rust Installation: rustup, toolchains, cargo
@icon: 🦀
@description: rustc, cargo new, rustup default, targets.
@order: 1

# Rust Installation: rustup, toolchains, cargo

`rustup` installs **rustc**, **cargo**, and **std** for your host triple. Manage **stable/beta/nightly** with `rustup toolchain`. Dependencies live in `Cargo.toml`.

@section: Basics
```bash
cargo new hello --bin
cd hello && cargo run
```

@quiz: Which tool manages Rust toolchains?
@option: pip
@correct: rustup
@option: cmake
