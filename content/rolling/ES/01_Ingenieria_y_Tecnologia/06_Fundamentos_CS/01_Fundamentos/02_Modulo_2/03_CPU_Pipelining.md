@title: Pipeline de CPU: hazards y predicción de saltos
@icon: ⚙️
@description: CPI, burbujas, forwarding, branch prediction y speculative execution.
@order: 3

# Pipeline: solapar etapas sin ensuciar el resultado

Un **pipeline** divide la ejecución en etapas (IF, ID, EX, MEM, WB). **Hazards** estructurales, de datos (RAW) y de control (branches) reducen IPC. **Forwarding** alimenta resultados antes del WB; **branch prediction** evita burbujas; **speculation** con **reorder buffers** en CPUs fuera de orden.

@section: CPI ideal

Pipeline profundo → mayor frecuencia potencial pero peor penalización por **missprediction**.

@section: Spectre/Meltdown

La ejecución especulativa dejó canales laterales; mitigaciones afectan rendimiento.

@section: Laboratorio sugerido

1. Mide impacto de `if` impredecible vs predecible en microbenchmark.
2. Lee sobre **branch target buffer** en manuales de arquitectura.
3. Simula pipeline de 5 etapas en papel con 3 instrucciones dependientes.

@quiz: ¿Qué tipo de hazard ocurre si una instrucción necesita un registro aún no escrito por la anterior?
@option: Estructural
@correct: Hazard de datos (RAW típico)
@option: Hazard de memoria virtual
