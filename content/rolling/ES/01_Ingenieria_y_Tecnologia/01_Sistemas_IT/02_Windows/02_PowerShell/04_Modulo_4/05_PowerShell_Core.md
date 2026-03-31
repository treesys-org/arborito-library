@title: PowerShell Core en Linux y MacOS
@icon: 🐧
@description: Llevando tus habilidades de PowerShell a otros sistemas operativos.
@order: 5

# PowerShell 7+ (PowerShell Core) multiplataforma

**PowerShell 7** (`pwsh`) funciona en **Windows, Linux y macOS**. Es **open-source** y comparte sintaxis con Windows PowerShell 5.1, pero algunos módulos **solo** existen en Windows.

@section: Instalación

*   **Linux:** paquetes `.deb`/`.rpm` o Microsoft repo.
*   **macOS:** Homebrew `brew install powershell`.

@section: Diferencias clave

*   Algunos cmdlets (`*-Service`, `*-EventLog`) dependen de **Windows** y no existen en Linux.
*   **Alternativas:** `Get-Process`, `Invoke-RestMethod`, herramientas nativas vía `bash`.

@section: SSH remoting

PowerShell 7 puede usar **SSH** como transporte para remoting en lugar de WinRM (configuración en `New-PSSession`).

@section: Windows PowerShell 5.1 vs pwsh

En Windows, **5.1** sigue siendo necesario para algunos módulos legacy. Puedes **llamar** `pwsh` desde `powershell.exe` o viceversa.

@section: CI/CD

Scripts **multiplataforma** en pipelines: `pwsh` en GitHub Actions, Azure DevOps, etc.

@section: Práctica

1.  Si tienes Linux en laboratorio, instala `pwsh` y ejecuta `$PSVersionTable`.
2.  Lista qué cmdlets de tu script favorito fallan en Linux y cómo sustituirlos.

@quiz: ¿Cuál es el nombre del ejecutable de PowerShell 7 multiplataforma?
@option: powershell.exe
@correct: pwsh
@option: pscore

@quiz: ¿Qué transporte remoto puede usarse en PowerShell 7 además de WinRM?
@option: FTP
@correct: SSH
@option: RDP
