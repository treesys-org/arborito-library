@title: Hyper-V: Virtualización Nativa
@icon: ☁️
@description: Creando y gestionando máquinas virtuales en Windows Server.
@order: 5

# Hyper-V: virtualización nativa

**Hyper-V** es el hipervisor de Microsoft integrado en Windows Server y Windows Pro/Enterprise (cliente). En un instituto de sistemas, es la base para **laboratorios**, **entornos de examen** y para entender **cómo se licencian** los hosts frente a las cargas de trabajo.

@section: Objetivos didácticos

*   Habilitar el rol Hyper-V y configurar **conmutadores virtuales**.
*   Crear una VM con **generación 2** (UEFI), discos VHDX y puntos de comprobación.
*   Distinguir **producción** vs **laboratorio** en el uso de puntos de restauración.

@section: Requisitos de hardware

*   **SLAT** y extensiones de virtualización en BIOS/UEFI (Intel VT-x/AMD-V).
*   RAM suficiente: RAM física ≥ suma RAM de VMs + host (regla práctica: no asignar más del 80–90 % de RAM a VMs en un portátil).
*   **Espacio en disco:** VHDX dinámicos o fijos según rendimiento.

@section: Instalación del rol

Desde Server Manager: **Roles → Hyper-V** → incluye **Herramientas de administración**.

PowerShell:

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
```

@section: Conmutadores virtuales (vSwitch)

1.  **Externo:** enlaza a la NIC física; la VM “sale” a la red como otro equipo.
2.  **Interno:** solo host ↔ VMs.
3.  **Privado:** solo entre VMs.

En un solo adaptador físico, **no** crees múltiples conmutadores externos sin diseño de NIC teaming; en producción se usa **SET** o **LBFO** según versión.

@section: Creación de una VM (conceptos)

*   **Generación 2:** UEFI, arranque SCSI, mejor rendimiento para sistemas modernos.
*   **VHDX:** discos hasta 64 TB, mejor integridad que VHD antiguo.
*   **Red:** asigna el vSwitch correcto antes del primer arranque.
*   **Integración:** **Integration Services** (time sync, heartbeat, backup).

@section: Puntos de comprobación (checkpoints)

*   **Producción:** en Windows Server 2016+ existe **checkpoint de producción** (VSS) frente al **checkpoint estándar**; en producción, evita depender de checkpoints como backup: usa **backup** de aplicación consistente.
*   **Laboratorio:** checkpoint antes de un cambio arriesgado está bien; documenta y **elimina** checkpoints antiguos.

@section: Rendimiento rápido

*   **vCPU:** no asignes más virtuales que núcleos físicos sin medir.
*   **Disco:** evita almacenar muchas VMs en un único HDD mecánico saturado.
*   **Red:** **SR-IOV** o **VMQ** en cargas de alto rendimiento (según hardware).

@section: Práctica

1.  Crea un conmutador **externo** y una VM **Gen2** con ISO de Windows Server.
2.  Instala **Integration Services** si el SO invitado lo requiere.
3.  Documenta: **RAM asignada**, **tipo de disco**, **nombre del vSwitch**.

@quiz: ¿Qué tipo de conmutador virtual Hyper-V debe usar una VM que necesita salir a la misma VLAN que los equipos físicos de la oficina?
@option: Privado
@correct: Externo (con la NIC física correcta)
@option: Interno únicamente

@quiz: ¿Qué formato de disco virtual recomienda Microsoft para nuevos despliegues en Hyper-V?
@option: VHD
@correct: VHDX
@option: ISO
