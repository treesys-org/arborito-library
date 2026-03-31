@title: Logical Operators and Loops
@icon: 🔄
@description: Repeating work and comparing values.
@order: 2

# Operators and loops

Use **`-eq`**, **`-match`**, **`-like`**, **`-contains`**, and loops **`foreach`**, **`while`**, plus pipeline **`ForEach-Object`**.

@section: foreach vs ForEach-Object

Language `foreach` is often clearer for collections already in memory; **`ForEach-Object`** streams items from the pipeline.

@section: Measure-Object

Summarize numeric properties (e.g., total file size).

@quiz: Which operator tests membership in a collection?
@option: -eq
@correct: -contains (or -in depending on order)
@option: -match

@quiz: Which cmdlet runs a script block per pipeline object?
@option: Sort-Object
@correct: ForEach-Object
@option: Where-Object
