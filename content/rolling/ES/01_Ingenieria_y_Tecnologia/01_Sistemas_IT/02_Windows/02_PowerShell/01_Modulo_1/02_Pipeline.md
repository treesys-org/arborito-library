@title: El Pipeline (|) y el Filtrado de Objetos
@icon: ⛓️
@description: La característica que hace a PowerShell tan potente: trabajar con objetos, no con texto.
@order: 2

# Pipeline: objetos, no texto

En PowerShell, el operador **`|`** pasa **objetos .NET** entre cmdlets, no cadenas de texto como en Unix clásico (salvo que conviertas explícitamente). Por eso **`Where-Object`**, **`Select-Object`** y **`Sort-Object`** son herramientas diarias.

@section: Objetivos didácticos

*   Filtrar con **`Where-Object`** (`$_` / `$PSItem`).
*   Proyectar columnas con **`Select-Object`**.
*   Entender **tipos** con `Get-Member`.

@section: Ejemplo básico

```powershell
Get-Process | Where-Object { $_.CPU -gt 5 } | Sort-Object CPU -Descending
```

`$_` representa el objeto actual en el bloque de script.

@section: Select-Object

```powershell
Get-Service | Select-Object -First 5 Name, Status, StartType
```

`-ExpandProperty` es útil para “desanidar” propiedades.

@section: Get-Member

```powershell
Get-Service bits | Get-Member
```

Muestra **propiedades** y **métodos** del tipo real: imprescindible para saber qué filtrar.

@section: Comparaciones

Operadores:

*   `-eq`, `-ne`, `-gt`, `-lt`
*   `-match` (regex), `-like` (comodines)

@section: Rendimiento

Para colecciones enormes, a veces conviene **filtrar en el cmdlet origen** (`Get-WinEvent -FilterXPath`) en lugar de traer todo a memoria.

@section: Práctica

1.  Lista procesos con **WorkingSet64** mayor que 200 MB.
2.  Usa `Get-Member` en `Get-LocalUser` y documenta tres propiedades interesantes.

@quiz: ¿Qué representa `$_` dentro de un bloque `Where-Object { ... }`?
@option: El nombre del cmdlet
@correct: El objeto actual que viaja por el pipeline
@option: Siempre el primer proceso

@quiz: ¿Qué cmdlet inspecciona el tipo .NET y los miembros de un objeto?
@option: Get-Type
@correct: Get-Member
@option: Format-Table
