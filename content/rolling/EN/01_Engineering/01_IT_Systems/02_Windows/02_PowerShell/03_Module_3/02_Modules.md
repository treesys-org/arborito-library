@title: Building Modules
@icon: 📚
@description: Packaging functions for reuse.
@order: 2

# PowerShell modules

Package functions in a **`.psm1`** with a **`.psd1` manifest** (`New-ModuleManifest`). Export only what you need via **`Export-ModuleMember`**.

@section: Import

```powershell
Import-Module .\MyModule.psd1 -Force
Get-Command -Module MyModule
```

@section: Publish

**PowerShell Gallery** for public modules; private feeds for enterprises.

@quiz: What extension does a module manifest use?
@option: .ps1
@correct: .psd1
@option: .json

@quiz: Which cmdlet controls exported functions from a module?
@option: Export-Function
@correct: Export-ModuleMember
@option: Out-Module
