@title: Windows Server Versions and Licensing
@icon: 💳
@description: Understanding the Standard, Datacenter, and Essentials editions.
@order: 1

# Windows Server editions and licensing (vocational level)

This unit matches a real **Windows systems administrator** path in SMB and enterprise environments. Choosing the right edition and license model avoids unnecessary cost and audit risk.

@section: Learning outcomes

*   Compare **Standard**, **Datacenter**, and **Essentials** for virtualization density and features.
*   Explain **per-core licensing** and **CALs** in procurement terms.
*   Map editions to typical roles: AD DS, Hyper-V, storage, clustering.

@section: Servicing and lifecycle

Windows Server ships on **LTSC** tracks. Always verify **mainstream/extended** support dates for your exact version (e.g. **Windows Server 2022**) in official Microsoft documentation.

*   **Cumulative updates:** monthly security/quality patches scheduled in maintenance windows.
*   **Media:** Evaluation ISO from Microsoft Evaluation Center or volume-licensed images for labs.

@section: Key editions

### Standard

*   Most infrastructure roles (AD DS, DNS, DHCP, files, IIS, etc.).
*   Check Microsoft’s **virtualization rights** table for your version—**do not guess** in production.

### Datacenter

*   Designed for **high VM density** and advanced software-defined datacenter features (verify the feature matrix for your release).
*   Often chosen when per-core economics beat stacking many Standard licenses on a virtualization cluster.

### Essentials

*   Very small business scenarios with built-in limits; verify availability for your target OS version.

@section: Core licensing and CALs

Typical model since Windows Server 2016:

1.  **Server licenses** covering **all physical cores** (minimum cores per CPU per Microsoft rules).
2.  **CALs (Client Access Licenses):** per **user** or **device** accessing server services—not the same as “we bought Windows on the server.”

@section: Classroom reflection

1.  List three roles you will deploy (e.g. AD, DNS, files).
2.  Decide bare metal vs **Hyper-V host** and whether you need **multiple virtualization hosts**.
3.  Write two sentences recommending **Standard vs Datacenter** for your scenario.

@quiz: Which license grants a user the right to use services on a Windows Server (files, authentication, etc.)?
@option: OEM license for the PC
@option: Only the server OS license
@correct: CAL (user or device)
@option: Antivirus subscription

@quiz: Which edition is commonly selected for high-density virtualization on Windows Server Hyper-V clusters?
@option: Essentials
@correct: Datacenter
@option: Home Basic

@quiz: What must you verify before upgrading Windows Server in an existing domain?
@option: Wallpaper only
@correct: Role compatibility matrix, AD schema, and application support
@option: Disk color
