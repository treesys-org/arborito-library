@title: Cmdlets, Aliases, and the Help System
@icon: 📘
@description: The fundamental building blocks of PowerShell.
@order: 1

# Cmdlets, aliases, and help

PowerShell cmdlets follow **Verb-Noun** naming (`Get-Help`, `Get-Process`). Learn **`Get-Help`**, **`Get-Command`**, and parameter basics first—this is day-one on the job.

@section: Cmdlets

```powershell
Get-Service bits
Stop-Process -Name notepad -ErrorAction SilentlyContinue
```

@section: Help

```powershell
Get-Help Get-ChildItem -Examples
Update-Help   # requires admin + internet in many labs
```

@section: Get-Command

```powershell
Get-Command *EventLog*
```

@section: Aliases

`ls`, `dir`, `gps` are convenient but **avoid aliases** in shared scripts.

@section: Common parameters

`-ErrorAction`, `-Verbose`, `-WhatIf`, `-Confirm` when supported.

@quiz: Which cmdlet displays built-in documentation?
@option: man (only Linux)
@correct: Get-Help
@option: Get-Date

@quiz: Why avoid aliases like `ls` in production scripts?
@option: ls does not exist
@correct: Scripts become harder to read and may break across environments
@option: Aliases are slower
