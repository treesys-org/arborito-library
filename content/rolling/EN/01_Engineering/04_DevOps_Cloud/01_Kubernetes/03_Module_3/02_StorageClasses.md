
@title: StorageClasses and provisioning
@icon: 📦
@description: Dynamic provisioning, reclaimPolicy, default StorageClass.
@order: 2

# StorageClass

A **StorageClass** describes **how** volumes are created: CSI driver, disk type (SSD/HDD), encryption, expansion flags, etc. `provisioner` names the plugin (e.g. `ebs.csi.aws.com`).

@section: Dynamic provisioning

When a PVC names a `storageClassName`, the controller provisions a PV automatically. If the field is empty and a **default** StorageClass exists (`storageclass.kubernetes.io/is-default-class: "true"`), it is used.

@section: reclaimPolicy

*   **Delete:** removing the PVC deletes the PV and often the cloud disk (driver-dependent).
*   **Retain:** keeps the disk for manual recovery—common for critical data.

@section: VolumeBindingMode

`WaitForFirstConsumer` delays provisioning until a scheduled Pod needs the volume—avoids zones mismatched to the node.

@section: Expansion

Many drivers support **PVC expansion** (`allowVolumeExpansion: true`) without Pod recreation; the in-container filesystem may still need resize tooling.

@quiz: Which annotation marks a StorageClass as default for PVCs with empty storageClassName?
@option: kubernetes.io/default-storage
@correct: storageclass.kubernetes.io/is-default-class: "true"
@option: provisioner.kubernetes.io/default
