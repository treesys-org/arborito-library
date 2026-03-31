@title: Print Services and Driver Management
@icon: 🖨️
@description: Centralizing print queues and drivers.
@order: 5

# Print services

The **Print and Document Services** role hosts **shared queues**, **drivers**, and optional **Branch Office Direct Printing** features.

@section: Install role

Add **Print Server** role; share printers via **TCP/IP** ports or host printers.

@section: Drivers

Host **x64** and legacy **x86** drivers so clients can download the right architecture.

**Point and Print** restrictions are security-sensitive—follow current Microsoft guidance for your OS versions.

@section: Deploy via GPO

Publish printers to AD or deploy with **Group Policy Preferences**.

@quiz: Which Windows Server role installs the Print Server service?
@option: IIS
@correct: Print and Document Services
@option: WDS

@quiz: Why install additional driver architectures on the print server?
@correct: So clients of different CPU architectures can install the correct driver when connecting
@option: To speed up the CPU
@option: Cosmetic only
