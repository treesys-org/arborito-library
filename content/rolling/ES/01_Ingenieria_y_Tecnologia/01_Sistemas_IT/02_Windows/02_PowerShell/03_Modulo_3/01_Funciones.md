@title: Funciones Avanzadas y Parámetros
@icon: 📦
@description: Creando tus propias herramientas reutilizables.
@order: 1

# Funciones avanzadas y parámetros

Una **función avanzada** declara **`[CmdletBinding()]`**, acepta **pipeline**, y soporta **parámetros comunes** (`-Verbose`, `-WhatIf` si implementas lógica). Es el puente entre scripts rápidos y **módulos** reutilizables.

@section: CmdletBinding

```powershell
function Get-LabDiskInfo {
  [CmdletBinding()]
  param(
    [Parameter(Mandatory, ValueFromPipeline)]
    [string[]]$ComputerName
  )
  process {
    foreach ($c in $ComputerName) {
      Get-CimInstance -ClassName Win32_LogicalDisk -ComputerName $c
    }
  }
}
```

@section: Parámetros

*   **`[Parameter()]`**: `Mandatory`, `Position`, `ValueFromPipeline`, `ValidateSet`.
*   **`ValidateScript`** para validación custom.

@section: Splatting

```powershell
$params = @{ Name = 'bits'; ErrorAction = 'Stop' }
Get-Service @params
```

Limpia llamadas largas.

@section: Output

`Write-Output` o simplemente valores en la última línea del bloque. **`Write-Host`** no va al pipeline.

@section: Práctica

1.  Escribe una función que acepte `-Path` y cuente archivos `.log`.
2.  Añade `SupportsShouldProcess` para simular `-WhatIf` (opcional avanzado).

@quiz: ¿Qué atributo convierte una función en “avanzada” con parámetros comunes?
@option: [Function()]
@correct: [CmdletBinding()]
@option: [Advanced()]

@quiz: ¿Qué técnica permite pasar un hashtable de parámetros a un cmdlet?
@option: Pipeline
@correct: Splatting (@params)
@option: Hash-match
