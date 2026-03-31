@title: Installation, Sysprep, and Post-Configuration
@icon: 💿
@description: From boot media to first-server baseline.
@order: 2

# Installation, Sysprep, and post-configuration

Professional installs mean verified media, sane disk layout, correct **computer name** and **network** **before** promoting domain controllers, and **generalized** images for cloning without duplicate machine identities.

@section: Learning outcomes

*   Install **Server Core** vs **Desktop Experience** deliberately.
*   Use **Sysprep** to prepare cloneable images.
*   Complete baseline configuration: network, time, updates, firewall.

@section: Boot media and partitioning

*   **System volume:** dedicated (commonly 60–120 GB depending on roles/logs).
*   **Data / VMs / shares:** separate volumes when possible.
*   **Resilience:** hardware RAID or storage spaces per policy; in labs at least separate OS from data.

@section: Server Core vs Desktop Experience

*   **Core:** smaller attack surface, fewer GUI patches—great for AD, DNS, DHCP, files, Hyper-V.
*   **Desktop Experience:** full GUI; useful when you rely on local MMC tools or visual training.

**Industry pattern:** **Core + Windows Admin Center** from a management workstation.

@section: Sysprep (generalize)

Prepares the image for cloning; next boot runs **OOBE** or mini-setup.

**Do not** Sysprep a production DC—promote **after** deployment automation or follow Microsoft guidance for your scenario.

```text
sysprep /generalize /oobe /shutdown
```

@section: Post-install checklist

1.  **Computer name** standard (`SRV-DC01`, `SRV-FILES01`).
2.  **Static IP** and **DNS** (DCs must reference correct DNS—usually themselves or partner DCs).
3.  **Time zone** and reliable **NTP** (Kerberos is time-sensitive).
4.  **Patches** before promoting AD in production-like labs.
5.  **Firewall:** open minimum ports; avoid “disable firewall to test.”

@quiz: Why run Sysprep before cloning a reference Windows Server image?
@option: Clear browser history
@correct: Generalize the install and avoid duplicated security identifiers / machine state
@option: Install Linux drivers

@quiz: What must be correct before promoting a domain controller?
@option: Wallpaper
@correct: Computer name, static IP, and DNS pointing to the correct DNS service for the future domain
@option: Disable firewall with no plan
