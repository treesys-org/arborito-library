@title: Static Pods and the kubelet
@icon: 📌
@description: Filesystem manifests on the node, bootstrapping, and differences from DaemonSets.
@order: 4

# Static Pods: local kubelet control

A **static pod** is defined by a manifest file on the **node filesystem** and managed **only** by the **kubelet**, not the apiserver (though the apiserver may mirror it for visibility). Used for **control plane bootstrapping** (manifests under `/etc/kubernetes/manifests` with kubeadm) and special cases.

@section: How it works

You place YAML in the kubelet’s watched directory (`staticPodPath`). The kubelet creates the pod locally and **restarts** it if the process crashes, per normal policies.

The apiserver may show a **mirror pod** with a `-<nodename>` suffix for visibility.

@section: Differences vs DaemonSet

| Aspect | Static Pod | DaemonSet |
|--------|------------|-----------|
| Controlled by | kubelet on that node | control plane controller |
| Typical use | Control plane, bootstrapping | Per-node agents (CNI helper, logs) |
| Visibility | Node-local | Standard API |

**DaemonSet** is preferred for cluster-wide agents managed via API; **static pod** when the apiserver does not exist yet or for tight node coupling.

@section: Limitations

* Do not use static pods for business apps unless you have a strong reason.
* **SSH** management per node does not scale; pure GitOps is harder without extra tooling.

@section: Security

The manifest directory is **highly privileged**: restrict filesystem permissions and who can write. An attacker with access can escalate privileges.

@section: Debugging

**crictl** / **docker** on the node to inspect containers; kubelet logs if the pod does not appear. Check paths and YAML syntax (errors can prevent component startup).

@section: Common mistakes

* Confusing mirror pods with “normal” pods and deleting them via API without lasting effect.
* Same static pod name on multiple nodes without coordination.

@section: Suggested lab

1. On a non-production node, place a minimal static pod manifest and observe it with `kubectl get pods -A` (depending on configuration).
2. Delete the file and verify the pod disappears.
3. Document when your organization allows static pods vs DaemonSets.

@quiz: Who is the primary authority managing a static pod?
@option: The Deployment controller
@correct: The kubelet on the node where the manifest file lives
@option: etcd only
