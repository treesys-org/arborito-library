@title: Output Formatting (List, Table, GridView)
@icon: 📜
@description: Presenting data the way you need it.
@order: 3

# Formatting and exporting

Use **`Format-*`** for display. Use **`Export-Csv`**, **`ConvertTo-Json`** for persistence. **Do not** pipe `Format-Table` into `Export-Csv`.

@section: Format-Table

```powershell
Get-Service | Format-Table Name, Status -AutoSize
```

@section: Export-Csv

```powershell
Get-Process | Select-Object Name, Id, CPU | Export-Csv .\procs.csv -NoTypeInformation -Encoding UTF8
```

@quiz: Why is `Format-Table | Export-Csv` usually wrong?
@option: CSV does not support tables
@correct: Format-* emits formatting records, not the original objects
@option: Export-Csv is Linux-only

@quiz: Which parameter removes the type information line in CSV for Excel?
@option: -Delimiter
@correct: -NoTypeInformation
@option: -Append
