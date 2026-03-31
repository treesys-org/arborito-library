@title: Trabajando con Archivos, CSV y JSON
@icon: 📄
@description: Cómo leer y escribir datos en diferentes formatos.
@order: 5

# Archivos, CSV y JSON

**`Get-Content`**, **`Set-Content`**, **`Add-Content`** para texto. **`Import-Csv`** / **`Export-Csv`** para hojas. **`ConvertFrom-Json`** / **`ConvertTo-Json`** para APIs modernas.

@section: Leer y escribir

```powershell
Get-Content -Path .\log.txt -Tail 50
Set-Content -Path .\out.txt -Value 'linea'
```

**`-Encoding UTF8`** para caracteres internacionales.

@section: CSV

```powershell
Import-Csv .\users.csv | Where-Object { $_.Department -eq 'IT' }
```

Requiere **cabecera** de columnas. Si no hay cabecera, usa `-Header`.

@section: JSON

```powershell
$j = Invoke-RestMethod -Uri 'https://api.github.com/repos/microsoft/vscode'
$j | ConvertTo-Json -Depth 5
```

**`-Depth`** evita truncar objetos anidados.

@section: XML

`[xml]` y `Select-Xml` para configuraciones legacy.

@section: Rutas

`Join-Path`, `Split-Path`, `Resolve-Path` — evita concatenar strings con `\` manualmente.

@section: Práctica

1.  Exporta procesos a CSV con **Name, Id, CPU**.
2.  Lee el CSV y filtra por **CPU > 1**.

@quiz: ¿Qué cmdlet importa un archivo CSV con primera fila de cabecera?
@option: Get-Content
@correct: Import-Csv
@option: ConvertFrom-Json

@quiz: ¿Por qué se usa -Depth con ConvertTo-Json en objetos grandes?
@option: Para borrar el historial
@correct: Para controlar cuántos niveles de objetos anidados se serializan
@option: Para cifrar JSON
