@title: Virtualización KVM/QEMU y Libvirt
@icon: ☁️
@description: Convierte tu Linux en un Hypervisor de nivel empresarial. Crea máquinas virtuales nativas sin necesidad de VirtualBox o VMware.
@order: 4

# KVM, QEMU y libvirt: hipervisor de producción

**KVM** convierte el kernel en hipervisor; **QEMU** emula hardware; **libvirt** (`virsh`, `virt-manager`) gestiona dominios, redes y almacenamiento. Es el stack de **OpenStack**, muchas nubes y laboratorios **LPIC-2**.

@section: 1. Requisitos y comprobación

```bash
grep -E 'vmx|svm' /proc/cpuinfo
lsmod | grep kvm
```

Instalación típica (Debian):

```bash
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
sudo adduser $USER libvirt && newgrp libvirt
```

**RHEL:** paquetes `qemu-kvm`, `libvirt`; servicio `libvirtd`; SELinux booleans `virt_*`.

@section: 2. Redes: NAT, bridge y macvtap

*   **default (virbr0):** NAT aislado; VMs salen a Internet pero no reciben tráfico entrante directo sin port forwarding.
*   **Bridge br0** en NIC física: la VM es ciudadano de la LAN (necesario para servicios accesibles desde fuera).
*   **macvtap:** puente a nivel L2 con restricciones de comunicación host↔guest según modo.

`virsh net-list --all`, `virsh net-edit default`.

@section: 3. Almacenamiento: qcow2 vs raw, pools

*   **qcow2:** instantáneas, thin provisioning, overhead moderado.
*   **raw:** rendimiento máximo en algunos casos.

**Pools:** `virsh pool-define-as`, `virsh vol-create-as`. Rutas habituales `/var/lib/libvirt/images`.

@section: 4. virsh esencial

```bash
virsh list --all
virsh dominfo nombre
virsh start|shutdown|destroy nombre
virsh console nombre          # requiere consola serial en la VM
virsh dumpxml nombre > backup.xml
```

**Snapshots:** `virsh snapshot-create-as` (qcow2 externo/interno).

@section: 5. virt-install (despliegue automatizado)

```bash
virt-install --name srv01 --memory 2048 --vcpus 2 \
  --disk size=20 --os-variant ubuntu22.04 \
  --network bridge=br0 --location http://... \
  --noautoconsole
```

**cloud-init** con imágenes cloud (`ubuntu-server-cloudimg`) para clonado masivo.

**XML de dominio:** `virsh dumpxml` describe CPU, memoria, discos, red y features (ACPI, APIC). Versionar ese XML en Git es una forma barata de **infraestructura como código** antes de Terraform o roles Ansible complejos.

@section: 6. Migración y rendimiento

*   **Migración en vivo** requiere almacenamiento compartido y mismas CPUs (o migración CPU compatible).
*   **Huge pages** y **CPU pinning** para cargas de baja latencia.
*   **virtio** drivers para disco y red en el invitado.

@section: 7. Diagnóstico

```bash
journalctl -u libvirtd -e
virt-host-validate
```

**Nested virtualization:** ejecutar KVM dentro de VMs (útil en CI). Requiere flags en el hipervisor y suele desactivarse en nube pública por seguridad o coste.

**Seguridad:** **sVirt** (SELinux + libvirt) etiqueta procesos QEMU y recursos; revisa denegaciones AVC si mueves imágenes de disco fuera de rutas esperadas.

@section: 8. Laboratorio ampliado

1.  Crea un bridge `br0` y una VM conectada a él; prueba ping desde otra máquina en la misma LAN.
2.  Haz snapshot antes de un cambio arriesgado y restaura; comprueba que el servicio vuelve al estado previo.
3.  Exporta `dumpxml` de una VM y documenta qué cambiarías para añadir un segundo disco **virtio**.
4.  Compara el uso de CPU con **virtio** vs emulación IDE antigua (laboratorio).

@quiz: ¿Qué módulo del kernel permite la virtualización asistida por hardware en Linux?
@option: Xen
@correct: kvm (Intel VT-x / AMD-V)
@option: vboxdrv

@quiz: ¿Qué herramienta gestiona dominios, redes y pools desde XML?
@option: qemu-system-x86_64 solo
@correct: libvirt (virsh)
@option: docker

@quiz: ¿Qué formato de disco suele soportar snapshots internos en libvirt?
@option: raw siempre
@correct: qcow2
@option: iso
