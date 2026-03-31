@title: User, Group, and OU Management
@icon: 🧑‍💼
@description: Creating identities and organizing permissions.
@order: 3

# Users, groups, and OUs

Assign permissions via **security groups**, not one-off user ACLs. Use **OUs** for delegation and lifecycle (joiners/movers/leavers).

@section: Creating users

```powershell
New-ADUser -Name "Jane Doe" -SamAccountName "jdoe" `
  -UserPrincipalName "jdoe@contoso.com" -Path "OU=Sales,DC=contoso,DC=com" `
  -AccountPassword (ConvertTo-SecureString "TempPass!" -AsPlainText -Force) -Enabled $true
```

Force **password change at logon** for temps.

@section: Groups

Use **domain local** / **global** / **universal** correctly in multi-domain designs; in a **single domain**, keep it simple with **role-based global groups** mapped to ACLs.

@section: Delegation

OU → **Delegate Control** wizard for helpdesk tasks (password reset, join PCs) without Domain Admin.

@section: Lifecycle

Disable → remove group memberships → archive data → delete after policy retention.

@quiz: What is safer immediately after an employee leaves?
@option: Delete the account instantly
@correct: Disable the account, revoke sessions, remove memberships, then delete per policy
@option: Leave account enabled without password

@quiz: Why delegate on an OU?
@option: To install games
@correct: To grant limited admin tasks without full Domain Admin rights
@option: To delete the forest
