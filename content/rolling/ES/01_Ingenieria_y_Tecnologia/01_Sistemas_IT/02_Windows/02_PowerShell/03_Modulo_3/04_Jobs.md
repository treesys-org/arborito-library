@title: Jobs y Procesamiento en Segundo Plano
@icon: ⏳
@description: Ejecutando tareas largas sin bloquear tu consola.
@order: 4

# Jobs en PowerShell

Los **jobs** permiten trabajo **asíncrono**: `Start-Job` (local), `Invoke-Command -AsJob` (remoto), y en PowerShell 7+ **ForEach-Object -Parallel**.

@section: Start-Job

```powershell
$j = Start-Job -ScriptBlock { Start-Sleep 30; 'listo' }
Wait-Job $j
Receive-Job $j
Remove-Job $j
```

**Receive-Job** obtiene salida; puede necesitar `-Keep`.

@section: Jobs remotos

```powershell
Invoke-Command -ComputerName srv01 -ScriptBlock { Get-EventLog -LogName System -Newest 100 } -AsJob
Get-Job
Receive-Job -Id 1
```

@section: Parallel en PS 7+

```powershell
1..5 | ForEach-Object -Parallel {
  $_ * 2
} -ThrottleLimit 5
```

Requiere **PowerShell 7+**.

@section: Consideraciones

*   Los jobs **no comparten** el estado de la sesión interactiva igual que un script inline.
*   **Depuración** es más difícil: registra salida a archivo.

@section: Práctica

1.  Lanza un job local que cuente archivos en `C:\Windows\Temp`.
2.  Comprueba estado con `Get-Job`.

@quiz: ¿Qué cmdlet recupera la salida de un trabajo en segundo plano?
@option: Get-Output
@correct: Receive-Job
@option: Wait-Job

@quiz: ¿Qué parámetro de ForEach-Object (PS 7+) permite paralelismo?
@option: -Batch
@correct: -Parallel
@option: -Async
