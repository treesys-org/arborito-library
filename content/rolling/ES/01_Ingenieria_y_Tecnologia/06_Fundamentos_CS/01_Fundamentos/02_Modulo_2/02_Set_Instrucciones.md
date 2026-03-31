@title: ISA: formato de instrucciones y modos de direccionamiento
@icon: 🧮
@description: RISC vs CISC, extensiones SIMD, ABI y calling conventions.
@order: 2

# Conjunto de instrucciones: contrato hardware/software

La **ISA** define opcodes, registros visibles, modos de direccionamiento y modelo de memoria. **RISC** (ARM, RISC-V) favorece instrucciones simples y pipeline regular; **CISC** histórico (x86) usa micro-op internas. **SIMD** (AVX, NEON) acelera vectores. Esta lección enlaza **ABI**, **calling conventions** y **endianness**.

@section: Modos de direccionamiento

Inmediato, registro, indirecto, con desplazamiento; crítico para generar código de compiladores.

@section: Privilegios

Anillos o niveles **user/supervisor**; **syscall** transiciona al kernel.

@section: Extensiones

**Atomics** para sincronización; **crypto** instructions en ARMv8.

@section: Errores frecuentes

* Confundir **ILP** con paralelismo explícito de hilos.

@section: Laboratorio sugerido

1. Inspecciona `objdump -d` de una función C simple en x86-64 y anota prologo/epilogo.
2. Compara tamaño de binario RISC-V vs x86 para misma optimización.
3. Lee la documentación de calling convention **System V AMD64**.

@quiz: ¿Qué describe principalmente una ISA?
@option: El sistema operativo instalado
@correct: El contrato entre hardware y software sobre instrucciones, registros y memoria
@option: Solo la velocidad del reloj
