@title: PowerShell Remoting (WinRM)
@icon: 🌐
@description: Running commands on remote hosts.
@order: 3

# WinRM remoting

Use **`Invoke-Command`** for one-off remote blocks, **`New-PSSession`** for persistent sessions.

@section: Example

```powershell
Invoke-Command -ComputerName srv01 -ScriptBlock { Get-Service bits }
```

**Second hop** credential delegation is a classic issue—design around it with JEA or constrained endpoints when possible.

@quiz: Which underlying service does Windows PowerShell remoting use?
@option: SSH by default
@correct: WinRM
@option: Telnet

@quiz: Which cmdlet runs a script block on remote machines?
@option: Enter-Only
@correct: Invoke-Command
@option: Remote-Run
