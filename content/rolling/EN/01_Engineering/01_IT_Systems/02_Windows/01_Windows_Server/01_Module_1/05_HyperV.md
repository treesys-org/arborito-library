@title: Hyper-V: Native Virtualization
@icon: ☁️
@description: Creating and managing virtual machines on Windows Server.
@order: 5

# Hyper-V native virtualization

**Hyper-V** is Microsoft’s type-1 hypervisor role. Vocational labs use it for coursework; enterprises use it for **density**, **test environments**, and understanding **licensing** vs workloads.

@section: Learning outcomes

*   Install Hyper-V and create **external/internal/private vSwitches**.
*   Deploy **Generation 2** VMs with **VHDX** disks.
*   Differentiate **production checkpoints** vs casual lab snapshots.

@section: Hardware prerequisites

*   Virtualization extensions enabled in firmware (**Intel VT-x / AMD-V**).
*   Enough **RAM** for host + guests.
*   Fast disk subsystem for multiple VMs.

@section: Install role

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
```

@section: Virtual switches

1.  **External:** binds to a physical NIC—VMs appear on the LAN.
2.  **Internal:** host ↔ VMs only.
3.  **Private:** VM ↔ VM only.

Avoid multiple external switches on one NIC without teaming design.

@section: VM creation essentials

*   **Generation 2:** UEFI, better performance for modern OS guests.
*   **VHDX:** large, resilient virtual disks.
*   **Integration Services:** time sync, heartbeat, backup awareness.

@section: Checkpoints

Use **production checkpoints** where supported; **never** treat checkpoints as backups—use application-consistent backup solutions.

@quiz: Which virtual switch type lets VMs reach the same network as physical PCs through a selected NIC?
@option: Private only
@correct: External
@option: Internal only

@quiz: Which virtual disk format is recommended for new Hyper-V deployments?
@option: VHD
@correct: VHDX
@option: ISO
