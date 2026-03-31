@title: PowerShell Core on Linux and macOS
@icon: 🐧
@description: Cross-platform PowerShell 7+.
@order: 5

# PowerShell 7 (`pwsh`)

**PowerShell 7** runs on **Windows, Linux, and macOS**. Some Windows-only cmdlets are unavailable—use native tools or REST.

@section: SSH remoting

PS7 supports **SSH** as a remoting transport alongside WinRM.

@section: Side-by-side

Windows **PowerShell 5.1** remains for some legacy modules; call `pwsh` for cross-platform scripts.

@quiz: What is the executable name for PowerShell 7?
@option: powershell.exe
@correct: pwsh
@option: pscore

@quiz: Besides WinRM, which remote transport can PS7 use?
@option: FTP
@correct: SSH
@option: RDP
