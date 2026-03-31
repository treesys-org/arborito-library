
@title: Volumes and PersistentVolumeClaims
@icon: 💾
@description: emptyDir, hostPath, PVC/PV lifecycle, access modes.
@order: 1

# Pod storage

Containers are ephemeral: without volumes, data is lost on restart. Kubernetes abstracts storage with **volumes** mounted into Pods and, for durable data, **PersistentVolume (PV)** + **PersistentVolumeClaim (PVC)**.

@section: Ephemeral volumes

*   **emptyDir:** empty directory on the node (or tmpfs with `medium: Memory`); lost when the Pod moves.
*   **hostPath:** mounts a host path—OK for single-node/dev; **risky** multi-tenant (data leakage between workloads).

@section: PVC and PV

Users declare a **PVC** (size, `storageClassName`, `accessModes`). Dynamic provisioning creates a **PV**, or a static PV binds. Pods reference the PVC by name.

```yaml
accessModes:
  - ReadWriteOnce
```

`ReadWriteMany` depends on the backend (NFS, shared filesystem CSI).

@section: subPath

`subPath` mounts a subdirectory or single file inside a volume—handy when sharing one volume across containers with different paths.

@section: CSI snapshots

**VolumeSnapshot** enables consistent copies where the CSI driver supports it. Requires **VolumeSnapshotClass** and CRDs.

@quiz: Which object does a user create to request persistent storage for a Pod?
@option: PersistentVolume directly always
@correct: PersistentVolumeClaim
@option: emptyDir with a Gi size field
