@title: The Pipeline (|) and Object Filtering
@icon: ⛓️
@description: Working with objects—not plain text.
@order: 2

# Pipeline and objects

The pipeline passes **.NET objects**. Use **`Where-Object`**, **`Select-Object`**, **`Sort-Object`**, and **`Get-Member`** constantly.

@section: Example

```powershell
Get-Process | Where-Object { $_.CPU -gt 5 } | Sort-Object CPU -Descending
```

`$_` / `$PSItem` is the current object.

@section: Select-Object

```powershell
Get-Service | Select-Object -First 5 Name, Status, StartType
```

@section: Get-Member

```powershell
Get-Service bits | Get-Member
```

@quiz: What does `$_` represent in `Where-Object { ... }`?
@option: Cmdlet name
@correct: The current pipeline object
@option: always the first process

@quiz: Which cmdlet inspects members of an object?
@option: Get-Type
@correct: Get-Member
@option: Format-Table
