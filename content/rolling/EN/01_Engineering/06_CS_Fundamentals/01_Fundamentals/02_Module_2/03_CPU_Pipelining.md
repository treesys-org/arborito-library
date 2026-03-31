@title: CPU Pipelining: Hazards and Branch Prediction
@icon: ⚙️
@description: CPI, bubbles, forwarding, speculative execution.
@order: 3

# Pipelining: overlap stages without corrupting results

A **pipeline** splits execution into stages (IF, ID, EX, MEM, WB). **Hazards** — structural, data (RAW), control (branches) — reduce IPC. **Forwarding** feeds results before WB; **branch prediction** reduces bubbles; **speculation** with **reorder buffers** in out-of-order CPUs.

@section: Ideal CPI

Deeper pipelines → higher potential frequency but worse **misprediction** penalties.

@section: Spectre/Meltdown

Speculative execution created side channels; mitigations reduce performance.

@section: Suggested lab

1. Measure impact of unpredictable vs predictable `if` in a microbenchmark.
2. Read about **branch target buffers** in architecture manuals.
3. Paper-simulate a 5-stage pipeline with three dependent instructions.

@quiz: Which hazard occurs when an instruction needs a register not yet written by a prior instruction?
@option: Structural hazard
@correct: Data hazard (typical RAW)
@option: Virtual memory hazard
