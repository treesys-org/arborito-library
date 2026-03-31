@title: Cmdlets, Alias y el Sistema de Ayuda
@icon: 📘
@description: Los bloques de construcción fundamentales de PowerShell.
@order: 1

# Cmdlets, alias y ayuda integrada

**PowerShell** es un shell orientado a **objetos** (pipeline .NET). Los **cmdlets** siguen la convención **Verbo-Sustantivo** (`Get-Help`, `Get-Process`). En administración Windows, dominar **Get-Help**, **Get-Command** y la sintaxis de parámetros es el primer día de trabajo real.

@section: Objetivos didácticos

*   Invocar cmdlets con parámetros **posicionales** y **nombrados**.
*   Usar **Get-Help** con `-Examples` y `-Online`.
*   Entender **alias** (útil, pero no en scripts de producción).

@section: Cmdlets

Formato: **`Verbo-Sustantivo`**, aprobado por Microsoft (reduce sorpresas).

Ejemplos:

```powershell
Get-Service bits
Stop-Process -Name notepad -ErrorAction SilentlyContinue
```

@section: Ayuda

```powershell
Get-Help Get-ChildItem -Full
Update-Help   # requiere permisos y acceso a Internet (en laboratorio)
```

**`-Examples`** muestra casos típicos. **`-Parameter`** filtra por parámetro.

@section: Get-Command

Descubre qué existe en el sistema:

```powershell
Get-Command *EventLog*
Get-Command -Verb Get -Noun Service
```

@section: Alias

`ls`, `dir`, `gps` son alias de cmdlets largos. **No uses alias** en scripts compartidos: dificulta la lectura y rompe en otros hosts.

@section: Parámetros comunes

*   `-ErrorAction` (`Continue`, `Stop`, `SilentlyContinue`…)
*   `-Verbose`, `-Debug`, `-WhatIf`, `-Confirm` (cuando el cmdlet lo soporta)

@section: Práctica

1.  Ejecuta `Get-Help about_Execution_Policies`.
2.  Lista cmdlets del módulo **Microsoft.PowerShell.Management**.
3.  Anota en tu cuaderno la diferencia entre **cmdlet** y **función externa**.

@quiz: ¿Qué cmdlet muestra la documentación integrada de PowerShell?
@option: man (solo Linux)
@correct: Get-Help
@option: Get-Date

@quiz: ¿Por qué se desaconseja usar alias como `ls` en scripts de producción?
@option: Porque ls no existe
@correct: Porque reduce la claridad y puede no existir en todos los entornos o versiones
@option: Porque es más lento
