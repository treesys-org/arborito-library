@title: Automatización de AD y Servidores con PS
@icon: 🤖
@description: Ejemplos prácticos de uso de PowerShell para gestionar tu dominio.
@order: 4

# Automatización con Active Directory PowerShell

El módulo **ActiveDirectory** expone cmdlets como **`Get-ADUser`**, **`New-ADGroup`**, **`Set-ADAccountPassword`**, **`Move-ADObject`**. En operaciones repetitivas, **PowerShell** es más seguro que hacer clic manualmente.

@section: Instalación del módulo

En controlador o equipos con RSAT:

```powershell
Import-Module ActiveDirectory
```

@section: Consultas

```powershell
Get-ADUser -Filter { Department -eq 'Ventas' } -Properties Department
Search-ADAccount -AccountExpired -UsersOnly
```

**`-Filter`** usa sintaxis LDAP-like; **no** es `Where-Object` del lado servidor.

@section: Altas

```powershell
New-ADUser -Name 'Ana' -SamAccountName 'ana' -Path 'OU=Ventas,DC=lab,DC=local' `
  -AccountPassword (ConvertTo-SecureString 'TempPass!' -AsPlainText -Force) -Enabled $true
```

@section: Grupos y membresía

```powershell
Add-ADGroupMember -Identity 'GG-Ventas' -Members 'ana'
```

@section: Bulk

Importa CSV y **foreach** con `New-ADUser` (laboratorio: prueba en OU de prueba).

@section: Seguridad

*   **Credenciales** con `Get-Credential` o **gMSA** para scripts de servicio.
*   **Logging** de quién ejecutó el script (transcripción).

@section: Práctica

1.  Lista usuarios **deshabilitados** con `Search-ADAccount`.
2.  Mueve un usuario de OU con `Move-ADObject`.

@quiz: ¿Qué cmdlet busca cuentas de usuario caducadas o deshabilitadas?
@option: Get-LocalUser
@correct: Search-ADAccount
@option: Get-ADComputer only

@quiz: ¿Qué parámetro de Get-ADUser filtra en el servidor en lugar de traer todo a memoria?
@option: -Where
@correct: -Filter
@option: -Like
