@title: Variables, Types, and Arrays
@icon: 📦
@description: Storing and manipulating data in scripts.
@order: 1

# Variables and collections

Variables start with **`$`**. Type accelerators like `[int]` help enforce correctness. **Arrays** and **hashtables** structure automation data.

@section: Examples

```powershell
$name = "Lab"
[int]$x = 7
$arr = 1,2,3
$ht = @{ Role = 'DC'; Site = 'NYC' }
```

@section: Hashtables

```powershell
$ht['Role']
$ht.Role
```

@quiz: What prefix do PowerShell variables use?
@option: @
@correct: $
@option: %

@quiz: Which structure stores key/value pairs?
@option: Array only
@correct: Hashtable (@{ })
@option: Queue
