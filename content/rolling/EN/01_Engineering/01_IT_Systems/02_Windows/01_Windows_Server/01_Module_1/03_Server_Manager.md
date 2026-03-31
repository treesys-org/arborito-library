@title: Server Manager and MMC Consoles
@icon: 📊
@description: Day-to-day graphical administration tools.
@order: 3

# Server Manager and MMC consoles

**Server Manager** is the hub for roles/features. **MMC** snap-ins (`dnsmgmt.msc`, `dhcpmgmt.msc`, `dsa.msc`, `gpmc.msc`) remain daily tools in helpdesk and tier-2 operations.

@section: Learning outcomes

*   Install roles/features from Server Manager and PowerShell.
*   Launch and pin common **.msc** consoles.
*   Use **All Servers** view and understand remote management prerequisites.

@section: Add Roles and Features wizard

1.  **Manage → Add Roles and Features**
2.  Role-based installation → pick server (local/remote)
3.  Select role (e.g. **AD DS**); add **management tools** when prompted.
4.  Reboot if required.

PowerShell equivalent:

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

@section: MMC snap-ins

| Console | Purpose |
| :--- | :--- |
| `dnsmgmt.msc` | DNS zones and records |
| `dhcpmgmt.msc` | Scopes, reservations, options |
| `dsa.msc` | Active Directory Users and Computers |
| `gpmc.msc` | Group Policy objects |

**Tip:** `Win + R` → type `.msc` → Enter. Build a custom MMC via `mmc.exe`.

@section: Remote management

**Add other servers to manage** requires WinRM, firewall rules, and admin rights on targets. Many teams adopt **Windows Admin Center** for browser-based management alongside MMC.

@quiz: What file extension do Microsoft Management Console snap-ins use?
@option: .exe
@correct: .msc
@option: .dll

@quiz: What does `-IncludeManagementTools` install alongside a role in PowerShell?
@option: Games
@correct: Administrative tools (RSAT components tied to that role)
@option: Linux subsystem
