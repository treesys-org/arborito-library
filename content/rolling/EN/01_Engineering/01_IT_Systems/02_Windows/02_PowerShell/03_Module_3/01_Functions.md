@title: Advanced Functions and Parameters
@icon: 📦
@description: Building reusable tools.
@order: 1

# Advanced functions

Add **`[CmdletBinding()`]**, **`param()`** with attributes, **`process {}`** for pipeline input, and **`ValidateSet`** / **`ValidateScript`** for robust parameters.

@section: Example skeleton

```powershell
function Get-LabDisk {
  [CmdletBinding()]
  param(
    [Parameter(Mandatory, ValueFromPipeline)]
    [string[]]$ComputerName
  )
  process {
    foreach ($c in $ComputerName) {
      Get-CimInstance Win32_LogicalDisk -ComputerName $c
    }
  }
}
```

@section: Splatting

```powershell
$params = @{ Name = 'bits'; ErrorAction = 'Stop' }
Get-Service @params
```

@quiz: Which attribute enables common parameters like `-Verbose` on a function?
@option: [Function()]
@correct: [CmdletBinding()]
@option: [Advanced()]

@quiz: What passes a hashtable of parameters to a cmdlet?
@option: Pipeline
@correct: Splatting (@params)
@option: Hash-match
