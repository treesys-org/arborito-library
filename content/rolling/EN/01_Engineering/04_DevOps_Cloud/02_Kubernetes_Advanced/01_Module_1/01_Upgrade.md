@title: Cluster Version Upgrade
@icon: ⬆️
@description: Planning control plane and node upgrades, API compatibility, and change windows.
@order: 1

# Kubernetes cluster upgrades: avoid surprises in production

**Upgrades** are not a silent `apt upgrade`: they involve a **new control plane version**, possibly **new node versions**, **deprecated APIs**, and different **scheduler/kubelet** behavior. This lesson outlines the mental model: read release notes, upgrade in supported hops, validate workloads, and plan **rollback**.

@section: Version skew and compatibility

Kubernetes maintains **skew** rules between components (kube-apiserver, kubelet, kubectl). Before planning:

* Read destination release notes on **kubernetes.io** (behavior changes, removed APIs).
* Check your distribution matrix (EKS, GKE, AKS, kubeadm) for required **intermediate** minors.

**Practical rule:** do not skip multiple minors without provider confirmation.

@section: Control plane vs nodes

In most managed environments the **control plane** is upgraded by the provider or an internal process; you manage **node groups** or **MachineDeployments**. In **kubeadm** installs, documented order is usually: upgrade **control plane** first, then **nodes**, keeping skew within allowed bounds.

**DaemonSets** (CNI, monitoring) must be compatible with the new version before cutover.

@section: API deprecations

`kubectl convert` is not magic: migrate manifests away from removed **beta** APIs (legacy `extensions/v1beta1` Ingress, etc.) **before** upgrading. Use:

* `kubectl get ingress -A -o yaml` and check `apiVersion`.
* Tools like **pluto** or CI policies that fail on obsolete APIs.

@section: Node rollout strategy

* **Replacing** nodes (new ASG/NG) is often cleaner than in-place `apt upgrade` everywhere.
* **Cordon + drain** old nodes after evicting workloads respecting **PodDisruptionBudgets**.

A misconfigured **PDB** can block drain indefinitely: check `minAvailable` vs real replicas.

@section: Pre-upgrade testing

* **Staging** environment on the target version with the same charts/Operators.
* **Load tests** and automated smoke tests post-upgrade.
* Verify **CSI drivers**, **Ingress controllers**, and **service mesh** compatibility matrices.

@section: Rollback

In managed clouds control plane rollback may be **limited** or unavailable. Plan:

* Application image versions pinned by tag.
* **Velero** or etcd backups (see backup lesson) if you run the control plane yourself.

@section: Change window and communication

Document owner, success criteria (latency, error rate), and **abort criteria** (roll back if SLO breaks). Notify platform and product teams.

@section: Common mistakes

* Upgrading without reading deprecations → `CrashLoopBackOff` from broken CRDs.
* Forgetting to upgrade **CNI** or **metrics-server** tied to the version.
* Mixing very new `kubectl` with old clusters without testing scripts.

@section: Suggested lab

1. On a test cluster (kind, minikube, or cloud sandbox), bump **one** minor following your tool’s official guide.
2. Run `kubectl api-resources` before and after and note relevant differences.
3. Simulate `kubectl drain` on a test node with a sample app and PDB.

@quiz: What should you review in manifests before upgrading?
@option: Only pod sizes
@correct: Deprecated or removed APIs that may prevent resources from being recreated correctly
@option: Only kubectl theme color
