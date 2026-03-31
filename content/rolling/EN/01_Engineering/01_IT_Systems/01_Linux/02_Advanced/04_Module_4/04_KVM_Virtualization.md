@title: KVM/QEMU and Libvirt Virtualization
@icon: ☁️
@description: Turn Linux into an enterprise hypervisor. Create native VMs without VirtualBox or VMware.
@order: 4

# KVM, QEMU, and libvirt: production hypervisor

**KVM** turns the kernel into a hypervisor; **QEMU** emulates hardware; **libvirt** (`virsh`, `virt-manager`) manages domains, networks, and storage. This stack powers **OpenStack**, many clouds, and **LPIC-2** labs.

@section: 1. Requirements and checks

```bash
grep -E 'vmx|svm' /proc/cpuinfo
lsmod | grep kvm
```

Typical Debian install:

```bash
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
sudo adduser $USER libvirt && newgrp libvirt
```

**RHEL:** `qemu-kvm`, `libvirt`; service `libvirtd`; SELinux booleans `virt_*`.

@section: 2. Networking: NAT, bridge, macvtap

* **default (virbr0):** isolated NAT; VMs reach the internet but need port forward for inbound.
* **Bridge br0** on a physical NIC: VM is a first-class LAN citizen.
* **macvtap:** L2 attachment with host↔guest restrictions depending on mode.

`virsh net-list --all`, `virsh net-edit default`.

@section: 3. Storage: qcow2 vs raw, pools

* **qcow2:** snapshots, thin provisioning, moderate overhead.
* **raw:** maximum performance in some workloads.

**Pools:** `virsh pool-define-as`, `virsh vol-create-as`. Default path often `/var/lib/libvirt/images`.

@section: 4. Essential virsh

```bash
virsh list --all
virsh dominfo name
virsh start|shutdown|destroy name
virsh console name          # needs serial console in guest
virsh dumpxml name > backup.xml
```

**Snapshots:** `virsh snapshot-create-as` (qcow2 internal/external).

@section: 5. virt-install

```bash
virt-install --name srv01 --memory 2048 --vcpus 2 \
  --disk size=20 --os-variant ubuntu22.04 \
  --network bridge=br0 --location http://... \
  --noautoconsole
```

**cloud-init** with cloud images for mass deployment.

**Domain XML:** `virsh dumpxml` describes CPU, memory, disks, NICs, and features (ACPI, APIC). Versioning that XML in Git is lightweight **infrastructure as code** before heavier Terraform/Ansible flows.

@section: 6. Migration and tuning

* **Live migration** needs shared storage and compatible CPUs (or explicit policies).
* **Huge pages**, **CPU pinning** for low latency.
* **virtio** disk and NIC drivers in the guest.

@section: 7. Diagnostics

```bash
journalctl -u libvirtd -e
virt-host-validate
```

**Nested virtualization:** KVM inside VMs (useful for CI). Requires host flags and is often disabled on public cloud for security or cost.

**Security:** **sVirt** (SELinux + libvirt) labels QEMU processes and resources; check AVC denials if you move disk images outside expected paths.

@section: 8. Extended lab

1. Create `br0` and attach a VM; ping it from another host on the same LAN.
2. Snapshot before a risky change and roll back; confirm the service returns to the previous state.
3. Export `dumpxml` for a VM and document what you would change to add a second **virtio** disk.
4. Compare CPU usage with **virtio** vs legacy IDE emulation (lab).

@quiz: Which kernel module enables hardware-assisted virtualization on Linux?
@option: Xen
@correct: kvm (Intel VT-x / AMD-V)
@option: vboxdrv

@quiz: Which stack manages domains, networks, and pools from XML?
@option: qemu-system-x86_64 alone
@correct: libvirt (virsh)
@option: docker

@quiz: Which disk format commonly supports internal snapshots in libvirt?
@option: raw always
@correct: qcow2
@option: iso
