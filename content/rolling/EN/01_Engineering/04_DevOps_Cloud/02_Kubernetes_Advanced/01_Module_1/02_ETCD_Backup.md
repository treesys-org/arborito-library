@title: etcd Backup and Restore
@icon: 💾
@description: Snapshots, consistency, control plane restore, and periodic testing.
@order: 2

# etcd: backing up and restoring cluster state

**etcd** stores all cluster state: API objects, configuration, and metadata. If you lose etcd without a coherent backup, **rebuilding** the cluster is painful. This lesson covers snapshots, **consistency**, restore procedures, and why copying `/var/lib/etcd` by hand is not enough.

@section: etcd’s role

etcd is a distributed **key-value** store with **Raft** consensus. Only etcd cluster members should access data; the apiserver is the main client.

**High availability:** typically 3 or 5 odd members across failure domains.

@section: Official snapshots

The **`etcdctl snapshot save`** tool creates a snapshot file at a consistent point when used correctly against the TLS-enabled cluster endpoint.

Conceptual steps:

1. Use correct **certificates** and **endpoints**.
2. Store snapshots in **external** storage (S3, GCS) with encryption and retention.
3. **Test restores** in an isolated environment, not only “trust the file.”

@section: Frequency and RPO

Define **recovery point objective (RPO)**: how much state can you lose? Hourly vs daily snapshots change risk. Combine snapshots with **GitOps** configuration backups for redeployable manifests.

@section: Restore

Restore is **not** always “replace folder and start”: it depends on stacked vs external etcd, and your tool (kubeadm, cloud operators).

**Typical procedure:** stop apiserver/kube-scheduler/controller-manager on affected members, `etcdctl snapshot restore`, reconfigure `--initial-cluster`, start in documented order.

**Important:** restore can impact the **entire** cluster; coordinate window and communication.

@section: Velero and workload backups

**Velero** backs up Kubernetes resources and volumes (depending on drivers); it complements etcd for **namespace recovery** or migrations. It does not fully replace etcd snapshots for full control-plane disaster scenarios.

@section: Encryption at rest

Enable **encryption at rest** on kube-apiserver for sensitive secrets in addition to etcd backups (defense in depth).

@section: Common mistakes

* Snapshots without verified restore (corrupt or useless files).
* Mixing snapshots from different members without understanding **quorum**.
* Keeping backups only in the same site as the cluster without off-site copy.

@section: Suggested lab

1. In a lab cluster with accessible etcd, run a snapshot and document exact commands.
2. Restore to **another** test cluster following official guidance.
3. Record measured total recovery time (RTO).

@quiz: Why is manually copying /var/lib/etcd without the etcdctl procedure risky?
@option: It uses too much disk
@correct: You need a consistent snapshot and a restore procedure compatible with Raft topology and TLS
@option: Kubernetes does not use etcd
