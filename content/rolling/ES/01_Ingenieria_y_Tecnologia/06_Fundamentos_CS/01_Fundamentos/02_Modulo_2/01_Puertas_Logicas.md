@title: Puertas lógicas y álgebra booleana
@icon: 🔲
@description: AND/OR/NOT/XOR, tablas de verdad y simplificación con Karnaugh.
@order: 1

# Puertas lógicas: del bit al circuito combinatorio

Las **puertas** AND, OR, NOT, XOR implementan **álgebra booleana** en hardware. Combinadas forman **sumadores**, **multiplexores**, **decodificadores**. Esta lección repasa **tablas de verdad**, **leyes de De Morgan**, y **mapas de Karnaugh** para minimizar expresiones antes de fabricar en **CMOS**.

@section: CMOS básico

**PMOS** y **NMOS** en redes complementarias: bajo consumido estático en estados estables; consumo dinámico al conmutar **capacitancias parásitas**.

@section: Retardos

**Critical path** limita frecuencia máxima; **fan-out** y longitud de cable importan.

@section: Diseño

**Half/full adder**, **ALU** slice, **LUT** en FPGAs.

@section: Laboratorio sugerido

1. Simula en Logisim un sumador de 4 bits con ripple-carry.
2. Aplica De Morgan para convertir NAND-only.
3. Minimiza una función 3-var con mapa K.

@quiz: ¿Qué ley booleana relaciona NOT(A AND B) con (NOT A) OR (NOT B)?
@option: Ley distributiva
@correct: Leyes de De Morgan
@option: Ley conmutativa únicamente
