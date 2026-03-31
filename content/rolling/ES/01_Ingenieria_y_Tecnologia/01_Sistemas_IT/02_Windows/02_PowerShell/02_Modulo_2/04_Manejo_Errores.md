@title: Manejo de Errores: Try, Catch, Finally
@icon: 🥅
@description: Escribiendo scripts robustos que no se rompen a la primera de cambio.
@order: 4

# Manejo de errores: try/catch y ErrorAction

PowerShell distingue errores **terminantes** y **no terminantes**. Solo los **terminantes** disparan **`catch`** salvo que fuerces **`ErrorAction Stop`**.

@section: try / catch / finally

```powershell
try {
  Get-Item 'C:\noexiste.txt' -ErrorAction Stop
} catch {
  Write-Warning $_.Exception.Message
} finally {
  # limpieza
}
```

`$_` en catch es el **ErrorRecord**.

@section: ErrorActionPreference

```powershell
$ErrorActionPreference = 'Stop'
```

Afecta a cmdlets que respetan **-ErrorAction**.

@section: $?

Variable automática booleana; indica éxito del último comando, pero **no** sustituye un manejo completo.

@section: Logging

```powershell
Write-Error "Fallo controlado"
```

En producción, usa **Transcript** o **logging** estructurado a archivo/Event Log.

@section: Buenas prácticas

*   **No** silencies todo con `SilentlyContinue` sin registrar el motivo.
*   Devuelve **códigos de salida** explícitos en scripts de orquestación (`exit 1`).

@section: Práctica

1.  Crea un `try/catch` alrededor de `Invoke-WebRequest` a una URL inválida.
2.  Registra el mensaje en `C:\temp\ps-errors.log` con `Add-Content`.

@quiz: ¿Qué valor de -ErrorAction hace que un error no terminante sea capturable por try/catch?
@option: SilentlyContinue
@correct: Stop
@option: Ignore

@quiz: ¿Qué objeto recibes en el bloque catch al inspeccionar el detalle?
@option: String simple
@correct: ErrorRecord (propiedad Exception, etc.)
@option: Hashtable
