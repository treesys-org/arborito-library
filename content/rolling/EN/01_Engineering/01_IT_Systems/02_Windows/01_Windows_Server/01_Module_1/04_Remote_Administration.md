@title: Remote Administration: RSAT and Windows Admin Center
@icon: 📡
@description: Managing servers without standing in the datacenter.
@order: 4

# Remote administration: RSAT and Windows Admin Center

Admins rarely touch keyboards in the rack. You use **RSAT** on a workstation, **Windows Admin Center** in a browser, and **PowerShell Remoting** (WinRM) for automation.

@section: Learning outcomes

*   Install **RSAT** features on Windows 10/11 Pro/Enterprise.
*   Connect **Windows Admin Center** to managed servers.
*   Understand **WinRM** basics and firewall requirements.

@section: RSAT

RSAT installs MMC tools on a **client** OS.

**Windows 10/11:** Settings → Apps → Optional features → add **RSAT** components (DNS, DHCP, GPO, AD tools, etc.).

**Requirement:** delegated rights or domain admin depending on task—follow least privilege.

@section: Windows Admin Center (WAC)

Lightweight web console for servers, clusters, and extensions.

1.  Install WAC on a management machine.
2.  Browse to the HTTPS endpoint.
3.  **Add** server by FQDN/IP with appropriate credentials.

@section: WinRM / PowerShell remoting

```powershell
Get-Service WinRM
# Enable-PSRemoting -Force   # lab only; follow org policy
```

Corporate environments usually enable WinRM via **GPO**, not ad hoc per server.

@section: Remote Desktop (RDP)

Still common for break/fix and GUI apps. Harden with **restricted groups**, **NLA**, and **no public exposure** without VPN/gateway.

@quiz: What package installs AD/DNS/DHCP MMC tools on Windows 10/11 clients?
@option: Visual Studio
@correct: RSAT
@option: Office 365

@quiz: Which service underpins PowerShell remoting on Windows?
@option: SSH by default
@correct: WinRM
@option: Telnet
