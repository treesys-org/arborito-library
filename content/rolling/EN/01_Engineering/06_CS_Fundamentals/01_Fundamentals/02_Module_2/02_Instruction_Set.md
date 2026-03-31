@title: ISA: Instruction Formats and Addressing Modes
@icon: 🧮
@description: RISC vs CISC, SIMD extensions, ABI, calling conventions.
@order: 2

# Instruction set architecture: hardware/software contract

An **ISA** defines opcodes, visible registers, addressing modes, and the memory model. **RISC** (ARM, RISC-V) favors simple instructions and regular pipelines; historical **CISC** (x86) uses internal micro-ops. **SIMD** (AVX, NEON) accelerates vectors. This lesson links **ABI**, **calling conventions**, and **endianness**.

@section: Addressing modes

Immediate, register, indirect, displacement-based — critical for compiler code generation.

@section: Privilege levels

User/supervisor rings; **syscall** transitions into the kernel.

@section: Extensions

**Atomic** instructions for synchronization; **crypto** instructions on ARMv8.

@section: Common mistakes

* Confusing **ILP** with explicit thread parallelism.

@section: Suggested lab

1. Inspect `objdump -d` of a simple C function on x86-64 and note prologue/epilogue.
2. Compare RISC-V vs x86 binary sizes for the same optimization level.
3. Read **System V AMD64** calling convention documentation.

@quiz: What does an ISA primarily describe?
@option: The installed operating system
@correct: The contract between hardware and software about instructions, registers, and memory
@option: Only clock speed
