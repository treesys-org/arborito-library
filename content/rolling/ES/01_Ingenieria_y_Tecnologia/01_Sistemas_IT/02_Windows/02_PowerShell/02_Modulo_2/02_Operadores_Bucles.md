@title: Operadores Lógicos y Bucles (ForEach, While)
@icon: 🔄
@description: Repitiendo acciones y comparando valores.
@order: 2

# Operadores y bucles

PowerShell ofrece operadores **`-eq`**, **`-match`**, **`-in`**, y bucles **`foreach`**, **`for`**, **`while`**, **`do/while`**. Para administración, **pipeline + ForEach-Object** es tan habitual como el bucle `foreach` de lenguaje.

@section: Operadores de comparación

```powershell
3 -eq 3          # True
"abc" -match '^a'
"foo","bar" -contains "foo"
```

**`-match`** usa regex; **`-like`** usa comodines (`*`).

@section: Operadores lógicos

`-and`, `-or`, `-not`, `-xor`.

@section: foreach (lenguaje)

```powershell
foreach ($s in Get-Service) {
  if ($s.Status -eq 'Running') { $s.Name }
}
```

@section: ForEach-Object (pipeline)

```powershell
Get-ChildItem *.log | ForEach-Object {
  Get-Content $_.FullName -ErrorAction SilentlyContinue
}
```

`%` es alias de `ForEach-Object`.

@section: while / do

```powershell
$i = 0
while ($i -lt 5) { $i++ }
```

Cuidado con **bucles infinitos** en scripts de producción.

@section: break / continue

Control de flujo estándar; **`break`** sale del bucle más interno.

@section: Práctica

1.  Cuenta cuántos servicios están en estado **Stopped**.
2.  Usa `Measure-Object` para sumar la propiedad **Length** de archivos `.log` en una carpeta.

@quiz: ¿Qué operador comprueba si una colección contiene un valor?
@option: -eq
@correct: -contains (o -in según orden)
@option: -match

@quiz: ¿Qué cmdlet procesa cada objeto del pipeline con un bloque de script?
@option: Sort-Object
@correct: ForEach-Object
@option: Where-Object
