@title: Variables, Tipos de Datos y Arrays
@icon: 📦
@description: Cómo almacenar y manipular información en tus scripts.
@order: 1

# Variables, tipos y colecciones

Las variables en PowerShell empiezan por **`$`**. El **tipo** puede ser **inferido** o **forzado** con `[type]`. Los **arrays** y **hashtables** estructuran datos en scripts de administración.

@section: Declaración

```powershell
$name = "Arborito"
[int]$port = 443
$list = @('a','b','c')
$hash = @{ Entorno = 'LAB'; Ciudad = 'Madrid' }
```

@section: Tipos comunes

`[string]`, `[int]`, `[bool]`, `[datetime]`, `[pscredential]` (usa `Get-Credential`).

@section: Arrays

```powershell
$nums = 1,2,3
$nums += 4
$nums[0]
$nums.Count
```

**ArrayList** o **List[T]** para grandes volúmenes (evita recrear el array al sumar).

@section: Hashtables

Acceso por clave:

```powershell
$hash['Entorno']
$hash.Entorno
```

Útiles para **lookup** rápido (mapear nombre de servidor → rol).

@section: Here-strings

```powershell
$sql = @"
SELECT *
FROM tabla
WHERE id = 1
"@
```

Preservan saltos de línea.

@section: Alcance

`$global:`, `$script:`, `$local:` — en módulos reutilizables, evita contaminar el ámbito global sin querer.

@section: Práctica

1.  Crea un hashtable de tres servidores con su IP.
2.  Itera con `foreach ($k in $hash.Keys)` y muestra clave/valor.

@quiz: ¿Qué prefijo tienen todas las variables en PowerShell?
@option: @
@correct: $
@option: %

@quiz: ¿Qué estructura usa pares clave-valor sin orden garantizado (por defecto)?
@option: Array
@correct: Hashtable (@{ })
@option: Queue
