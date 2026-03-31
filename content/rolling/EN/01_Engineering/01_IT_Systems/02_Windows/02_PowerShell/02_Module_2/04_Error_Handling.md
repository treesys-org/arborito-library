@title: Error Handling: Try, Catch, Finally
@icon: 🥅
@description: Building resilient scripts.
@order: 4

# Try/catch and ErrorAction

Non-terminating errors may **not** enter **`catch`** unless you set **`-ErrorAction Stop`** or **`$ErrorActionPreference = 'Stop'`**.

@section: Pattern

```powershell
try {
  Get-Item 'C:\missing.txt' -ErrorAction Stop
} catch {
  Write-Warning $_.Exception.Message
} finally {
  # cleanup
}
```

@section: Logging

Avoid blanket `SilentlyContinue` without recording why failures happened.

@quiz: Which `-ErrorAction` value makes errors catchable in `try/catch` for many cmdlets?
@option: SilentlyContinue
@correct: Stop
@option: Ignore

@quiz: What object type do you inspect in `catch`?
@option: plain string only
@correct: ErrorRecord (`.Exception`, etc.)
@option: Hashtable
