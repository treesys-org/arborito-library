@title: Node Maintenance: Cordon, Drain, and PDBs
@icon: 🔧
@description: Safely evicting workloads, grace periods, and scheduler coordination.
@order: 3

# Node maintenance: drain without killing the service

Before shutting down a node for patching or replacement you must **evict** workloads in a controlled way. **Cordon**, **drain**, **PodDisruptionBudgets**, and **grace periods** are the levers. This lesson explains the flow and typical mistakes when someone presses “reboot now.”

@section: Cordon

`kubectl cordon <node>` marks the node **NoSchedule**: it does not receive new pods, but existing pods keep running.

Use it as a first step before drain to avoid new replicas scheduling there.

@section: Drain

`kubectl drain <node>` tries to **evict** pods respecting rules:

* Ignores **DaemonSet** pods (they recreate) unless flags say otherwise.
* Respects **PDBs** when configured; otherwise it may block or force with risk.

Important flags:

* `--ignore-daemonsets`
* `--delete-emptydir-data` (careful with ephemeral local data)
* `--grace-period` aligned with how long the app needs to close connections.

@section: PodDisruptionBudget

A **PDB** limits how many replicas can be **unavailable** during voluntary disruptions.

Conceptual example: `minAvailable: 2` on a Deployment with three replicas → only one may disappear at a time.

**Problems:**

* Too strict PDB with low replica counts → impossible drain.
* `minAvailable` as a percentage with a small replica count → surprising rounding.

@section: Graceful shutdown

Applications should handle **SIGTERM**: stop listeners, drain requests, then exit. `terminationGracePeriodSeconds` must be long enough; otherwise Kubernetes kills the process after the deadline.

**Readiness** should remove the pod from the Service **before** the process dies (sometimes preStop patterns).

@section: Managed vs unmanaged nodes

In **managed node groups** the provider may replace nodes automatically; understanding drain still helps. On **bare metal** you own the full lifecycle.

@section: Common mistakes

* Draining without checking **local storage** (`emptyDir`) for critical data.
* Forgetting pods with **finalizers** can hang termination.
* Forcing `--force` without understanding which pods are lost.

@section: Suggested lab

1. Create a three-replica Deployment with a PDB `minAvailable: 2`.
2. Run `cordon` and `drain` on a test node and observe eviction order.
3. Misconfigure the PDB (impossible) and observe the block; fix with replicas or PDB.

@quiz: What does `kubectl cordon` do to a node?
@option: Deletes all pods immediately
@correct: Prevents new pods from being scheduled there; existing pods keep running
@option: Upgrades the kubelet
