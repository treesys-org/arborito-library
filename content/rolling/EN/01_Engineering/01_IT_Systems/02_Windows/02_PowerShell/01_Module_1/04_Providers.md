@title: PSProviders and PSDrives
@icon: 📦
@description: Navigating registry and certificates like drives.
@order: 4

# PSProviders and PSDrives

Providers expose **hierarchical stores** like **filesystem**, **registry**, **certificates**, **variables** as drives (`Get-PSDrive`).

@section: Examples

```powershell
Get-PSProvider
Set-Location Cert:\LocalMachine\My
Get-ChildItem | Select-Object Subject, Thumbprint
```

@section: New-PSDrive

Map a PSDrive to a network share for a session—document persistence behavior.

@quiz: Which drive exposes the certificate store?
@option: HKLM:
@correct: Cert:
@option: Variable:

@quiz: Which cmdlet lists providers?
@option: Get-Volume
@correct: Get-PSProvider
@option: Get-Service
