@title: AD Automation with PowerShell
@icon: 🤖
@description: Managing your domain from the shell.
@order: 4

# Active Directory module

**`Import-Module ActiveDirectory`** on DCs or RSAT machines. Use **`-Filter`** (server-side) instead of pulling everything.

@section: Examples

```powershell
Get-ADUser -Filter { Department -eq 'Sales' } -Properties Department
Search-ADAccount -AccountDisabled -UsersOnly
```

@section: Safety

Use **test OUs** for bulk imports; log changes with transcripts.

@quiz: Which cmdlet searches disabled or expired accounts?
@option: Get-LocalUser
@correct: Search-ADAccount
@option: Get-ADComputer only

@quiz: Which `Get-ADUser` parameter filters on the server?
@option: -Where
@correct: -Filter
@option: -Like
