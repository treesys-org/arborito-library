@title: Formateo de Salida (List, Table, GridView)
@icon: 📜
@description: Cómo mostrar los datos de la forma que necesitas.
@order: 3

# Formateo: List, Table, Wide y Out-GridView

Los cmdlets **`Format-*`** definen la presentación en consola. **`Export-*`** (CSV, XML, JSON) sirve para **persistir** datos. Confundir ambos es un error típico: **`Format-Table | Export-Csv`** no hace lo que imaginas.

@section: Objetivos didácticos

*   Usar **`Format-Table`**, **`Format-List`**, **`Format-Wide`**.
*   Crear vistas con **`Select-Object`** antes de formatear.
*   Exportar con **`Export-Csv -NoTypeInformation`**.

@section: Format-Table

```powershell
Get-Service | Sort-Object Status | Format-Table Name, Status, StartType -AutoSize
```

`-Wrap` evita truncar texto largo.

@section: Format-List

Útil para **muchos campos** o depuración:

```powershell
Get-ComputerInfo | Format-List
```

@section: Out-GridView

Interfaz gráfica de filtrado (requiere interfaz; no en Server Core remoto sin desktop):

```powershell
Get-Process | Out-GridView -PassThru
```

@section: Export-Csv / JSON

```powershell
Get-ADUser -Filter * -Properties SamAccountName, Department |
  Select-Object SamAccountName, Department |
  Export-Csv -Path C:\temp\users.csv -NoTypeInformation -Encoding UTF8
```

**UTF8** evita problemas con tildes en Excel.

@section: Trampa común

`Format-Table` produce objetos de **formato**, no los datos crudos. Para procesar más, **no formatees** hasta el final del pipeline.

@section: Práctica

1.  Exporta la lista de servicios **Running** a CSV.
2.  Abre el CSV en Excel y verifica codificación.

@quiz: ¿Por qué no debes usar Format-Table justo antes de Export-Csv en un pipeline?
@option: Porque CSV no soporta tablas
@correct: Porque Format-Table devuelve objetos de formato, no los objetos originales serializables
@option: Porque Export-Csv es solo para Linux

@quiz: ¿Qué parámetro de Export-Csv evita la primera línea de tipo en archivos para Excel?
@option: -Delimiter
@correct: -NoTypeInformation
@option: -Append
