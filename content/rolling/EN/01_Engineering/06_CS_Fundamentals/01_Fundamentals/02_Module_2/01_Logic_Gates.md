@title: Logic Gates and Boolean Algebra
@icon: 🔲
@description: AND/OR/NOT/XOR, truth tables, Karnaugh simplification.
@order: 1

# Logic gates: from bits to combinational circuits

**AND**, **OR**, **NOT**, **XOR** gates implement **boolean algebra** in hardware. Combined they build **adders**, **multiplexers**, **decoders**. This lesson reviews **truth tables**, **De Morgan’s laws**, and **Karnaugh maps** to minimize expressions before fabricating **CMOS**.

@section: Basic CMOS

Complementary **PMOS**/**NMOS** networks: low static power in steady state; dynamic power when switching **parasitic capacitances**.

@section: Delays

The **critical path** limits maximum frequency; **fan-out** and wire length matter.

@section: Design

**Half/full adder**, **ALU** slice, **LUT** in FPGAs.

@section: Suggested lab

1. Simulate a 4-bit ripple-carry adder in Logisim.
2. Apply De Morgan to convert to NAND-only.
3. Minimize a 3-variable function with a K-map.

@quiz: Which Boolean law relates NOT(A AND B) to (NOT A) OR (NOT B)?
@option: Distributive law
@correct: De Morgan’s laws
@option: Commutative law only
