
@title: Advanced Pod scheduling
@icon: 📍
@description: nodeSelector, affinity, taints, tolerations, priorities, PDBs.
@order: 5

# Scheduling

The **scheduler** assigns each pending Pod to a node. Beyond CPU/memory requests you can steer placement with **selectors**, **affinity**, **taints**, and **priorities**.

@section: nodeSelector and nodeName

`nodeSelector` is the simplest label filter on nodes. `nodeName` pins a Pod and **bypasses** the scheduler—special cases only.

@section: Affinity / anti-affinity

*   **requiredDuringSchedulingIgnoredDuringExecution:** hard; Pod stays **Pending** if unsatisfied.
*   **preferred…:** soft scoring.

Use **podAntiAffinity** to spread replicas across hosts; **podAffinity** to co-locate related workloads.

@section: Taints and tolerations

**Taints** on nodes **repel** Pods unless the Pod declares a matching **toleration**. Patterns: dedicated nodes (`gpu=true:NoSchedule`), isolation, or **drain** flows (`NoExecute`).

@section: Priority and preemption

**PriorityClass** can let high-priority Pods **preempt** others when resources are scarce—use carefully to avoid thrashing.

@section: PodDisruptionBudget

**PDB** limits how many Pods may be voluntarily disrupted at once during **drains** or controlled evictions—protects availability when tuned well.

@quiz: What mechanism lets a node reject Pods unless they explicitly tolerate it?
@option: nodeSelector alone
@correct: Node taints and Pod tolerations
@option: Only priorityClassName
