@title: PowerShell Remoting (WinRM)
@icon: 🌐
@description: Ejecutando comandos en cientos de máquinas a la vez.
@order: 3

# PowerShell Remoting (WinRM)

**WinRM** habilita sesiones remotas con **`Enter-PSSession`**, **`Invoke-Command`** y **sesiones persistentes** (`New-PSSession`). Es la base de la administración masiva en dominios Windows.

@section: Requisitos

*   WinRM habilitado en cliente/servidor (`Enable-PSRemoting`).
*   Firewall y **TrustedHosts** (workgroup) o **Kerberos** (dominio).
*   Credenciales con derechos.

@section: Invoke-Command

```powershell
Invoke-Command -ComputerName srv01, srv02 -ScriptBlock {
  Get-Service bits
}
```

**`-ThrottleLimit`** controla paralelismo.

@section: Sesión persistente

```powershell
$s = New-PSSession -ComputerName srv01
Invoke-Command -Session $s -ScriptBlock { hostname }
Remove-PSSession $s
```

Útil para múltiples comandos en el mismo contexto.

@section: Doble salto (second hop)

Problema clásico: credenciales no se delegan por defecto. Soluciones: **CredSSP** (con riesgos), **JEA**, o diseño sin segundo salto.

@section: JEA (visión)

**Just Enough Administration** limita qué cmdlets puede ejecutar un operador remoto.

@section: Práctica

1.  Ejecuta `Invoke-Command` en `localhost` con un bloque que liste el **OS caption**.
2.  Documenta el error típico si WinRM está deshabilitado.

@quiz: ¿Qué protocolo subyace a PowerShell Remoting en Windows?
@option: SSH (por defecto)
@correct: WinRM (Web Services Management)
@option: Telnet

@quiz: ¿Qué cmdlet ejecuta un bloque de script en uno o varios equipos remotos?
@option: Enter-Only
@correct: Invoke-Command
@option: Remote-Run
