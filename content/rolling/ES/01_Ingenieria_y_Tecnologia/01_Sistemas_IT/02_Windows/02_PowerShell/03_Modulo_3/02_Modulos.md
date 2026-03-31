@title: Creación de Módulos Propios
@icon: 📚
@description: Empaquetando y distribuyendo tus funciones.
@order: 2

# Módulos PowerShell

Un **módulo** agrupa funciones, manifiesto (`*.psd1`), y opcionalmente DLLs. Se distribuye como **carpeta** en `$env:PSModulePath`.

@section: Estructura mínima

```
MiModulo/
  MiModulo.psd1   # manifiesto
  MiModulo.psm1   # raíz del módulo (funciones)
```

`New-ModuleManifest` genera el **psd1**.

@section: Importación

```powershell
Import-Module .\MiModulo.psd1 -Force
Get-Command -Module MiModulo
```

@section: Export-ModuleMember

En **psm1**, exporta solo lo necesario:

```powershell
Export-ModuleMember -Function Get-LabDiskInfo
```

@section: Versionado

Actualiza **`ModuleVersion`** en el manifiesto; los repositorios corporativos (NuGet/Artifact feeds) versionan módulos como cualquier paquete.

@section: Publicación (concepto)

**PowerShell Gallery** (`Publish-Module`) para código abierto; en empresa, **repositorio privado**.

@section: Práctica

1.  Crea un módulo de laboratorio con una función `Get-ServerUptime`.
2.  Verifica con `Get-Module -ListAvailable`.

@quiz: ¿Qué extensión tiene el manifiesto de un módulo PowerShell?
@option: .ps1
@correct: .psd1
@option: .json

@quiz: ¿Qué cmdlet exporta funciones seleccionadas desde un archivo .psm1?
@option: Export-Function
@correct: Export-ModuleMember
@option: Out-Module
