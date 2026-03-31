@title: Using the .NET Framework
@icon: 🔗
@description: Calling .NET APIs directly from PowerShell.
@order: 5

# .NET from PowerShell

Instantiate types with **`New-Object`** or static methods with **`[Type]::Method()`**. Use **`Add-Type`** for embedded C# or P/Invoke.

@section: Example

```powershell
$sw = [System.Diagnostics.Stopwatch]::StartNew()
Start-Sleep -Seconds 1
$sw.Stop()
$sw.ElapsedMilliseconds
```

@quiz: How do you call a static .NET method?
@option: instance.method()
@correct: [ClassName]::Method()
@option: static.method()

@quiz: Which cmdlet compiles C# snippets in-session?
@option: Import-CSharp
@correct: Add-Type
@option: New-Class
