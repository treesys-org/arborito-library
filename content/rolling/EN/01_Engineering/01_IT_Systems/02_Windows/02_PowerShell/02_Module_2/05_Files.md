@title: Files, CSV, and JSON
@icon: 📄
@description: Reading and writing structured data.
@order: 5

# Files, CSV, JSON

**`Get-Content`/`Set-Content`**, **`Import-Csv`/`Export-Csv`**, **`Invoke-RestMethod`**, **`ConvertTo-Json`** cover most admin automation I/O.

@section: CSV

```powershell
Import-Csv .\users.csv | Where-Object Department -eq 'IT'
```

@section: JSON

Use **`-Depth`** with `ConvertTo-Json` for nested objects.

@section: Paths

Prefer **`Join-Path`** over manual `\` concatenation.

@quiz: Which cmdlet imports a CSV with a header row?
@option: Get-Content
@correct: Import-Csv
@option: ConvertFrom-Json

@quiz: Why use `-Depth` with `ConvertTo-Json`?
@option: Delete history
@correct: Control serialization depth for nested objects
@option: Encrypt JSON
