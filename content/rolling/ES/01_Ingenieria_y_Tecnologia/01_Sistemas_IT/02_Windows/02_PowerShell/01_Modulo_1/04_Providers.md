@title: PSProviders y PSDrives
@icon: 📦
@description: Navegando por el Registro o los Certificados como si fueran discos duros.
@order: 4

# PSProviders y PSDrives

Los **PSProviders** exponen almacenes jerárquicos (sistema de archivos, **registro**, **certificados**, **variables**) como si fueran **unidades**. **`Get-PSDrive`** lista letras; **`Set-Location`** navega.

@section: Objetivos didácticos

*   Listar providers con **`Get-PSProvider`**.
*   Navegar **`HKLM:`**, **`Cert:`** con `Get-ChildItem`.
*   Crear un **PSDrive** temporal mapeado a una ruta de red o registro.

@section: Concepto

Cada provider implementa **item**, **childitem**, **property** como en un sistema de archivos.

```powershell
Get-PSProvider
Set-Location HKLM:\SOFTWARE
Get-ChildItem
```

@section: Certificados

```powershell
Set-Location Cert:\LocalMachine\My
Get-ChildItem | Select-Object Subject, Thumbprint, NotAfter
```

@section: Registry

Útil para auditorías rápidas (con cuidado de no romper producción):

```powershell
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion'
```

@section: New-PSDrive

```powershell
New-PSDrive -Name Scripts -PSProvider FileSystem -Root \\fileserver\scripts
Set-Location Scripts:
```

`-Scope` y persistencia: muchos drives son **sesión**; documenta en scripts.

@section: Seguridad

Cambiar registro o certificados requiere **elevación** y **cambio controlado** (GPO preferible a scripts manuales dispersos).

@section: Práctica

1.  Navega `Env:` y muestra `$env:COMPUTERNAME`.
2.  Lista certificados en **My** del equipo local (laboratorio).

@quiz: ¿Qué cmdlet lista los PSProviders cargados en la sesión actual?
@option: Get-Volume
@correct: Get-PSProvider
@option: Get-Service

@quiz: ¿Qué unidad lógica expone el almacén de certificados del equipo?
@option: HKLM:
@correct: Cert:
@option: Variable:
