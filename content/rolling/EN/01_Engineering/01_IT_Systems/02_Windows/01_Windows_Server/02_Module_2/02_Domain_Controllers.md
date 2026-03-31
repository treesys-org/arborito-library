@title: Domain Controllers and FSMO Roles
@icon: 👑
@description: Servers that authenticate and replicate directory data.
@order: 2

# Domain controllers and FSMO roles

A **DC** runs **AD DS** and participates in **multi-master replication**, except for a handful of **FSMO** operations that must be single-sourced.

@section: Five FSMO roles

| Role | Scope | Summary |
| :--- | :--- | :--- |
| Schema Master | Forest | Schema updates |
| Domain Naming Master | Forest | Add/remove domains |
| PDC Emulator | Domain | Time, password lockout, etc. |
| RID Master | Domain | RID pools for new objects |
| Infrastructure Master | Domain | Cross-domain references |

View: `netdom query fsmo`

@section: Transfer vs seize

*   **Transfer:** coordinated during maintenance.
*   **Seize:** emergency when a DC is permanently lost—use with care.

@section: Minimum two DCs

A single DC is a **single point of failure** for authentication and DNS. Plan **two DCs** in production and **system state backups** of AD.

@section: RODC

**Read-only domain controllers** for branch offices with limited physical security—limited credential caching.

@quiz: Which FSMO role is central to Kerberos time skew tolerance?
@option: Schema Master
@correct: PDC Emulator
@option: RID Master

@quiz: When should you use role seizure instead of transfer?
@option: During Windows Update
@correct: When the role holder DC is permanently offline or broken
@option: Never
