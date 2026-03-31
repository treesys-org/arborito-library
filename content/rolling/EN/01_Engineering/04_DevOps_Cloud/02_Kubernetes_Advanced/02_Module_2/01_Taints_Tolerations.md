@title: Taints and Tolerations: Dedicated Nodes and Isolation
@icon: 🧲
@description: NoSchedule, PreferNoSchedule, NoExecute, and matching tolerations.
@order: 1

# Taints and tolerations: fine-grained scheduling control

**Taints** on a node **repel** pods unless they have a matching **toleration**. Use them for dedicated nodes (GPU, ingress), **spot** isolation, or nodes being drained gradually (`NoExecute`). This lesson explains effects, `effect` values, and how not to accidentally block all scheduling.

@section: Mental model

* **Taint on the node:** “only enter if you tolerate this.”
* **Toleration on the pod:** “I accept that taint.”

Without a toleration, the scheduler **does not** place the pod on incompatible tainted nodes (depending on effect).

@section: Effects

* **NoSchedule:** no new scheduling unless tolerated; running pods are not evicted by the taint alone.
* **PreferNoSchedule:** scheduler avoids the node if possible, but it is not strict.
* **NoExecute:** in addition to NoSchedule, **evicts** existing pods after `tolerationSeconds` (if specified) that do not tolerate.

**Typical NoExecute use:** mark nodes in maintenance or spot nodes about to disappear.

@section: Syntax

Example taint:

```text
dedicated=gpu:NoSchedule
```

Pod toleration fragment:

```yaml
tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

**Exists** vs **Equal:** `Exists` tolerates any value if the key matches.

@section: Default taints

**Control-plane/master** nodes often carry taints so normal workloads do not run unless explicitly tolerated (depending on version and configuration).

@section: Interaction with affinity

**Node affinity** **attracts** pods to nodes; **taints** **repel**. You can attract to a GPU pool and still repel workloads without tolerations.

@section: Common mistakes

* Wrong taint on all nodes → nothing schedules.
* Forgetting tolerations on DaemonSets that must run on tainted nodes.
* `NoExecute` without clear `tolerationSeconds` → pods disappear unexpectedly.

@section: Suggested lab

1. Apply a `NoSchedule` taint to a test node and observe new pods not scheduling.
2. Add a toleration to a Deployment and verify scheduling.
3. Try `NoExecute` with `tolerationSeconds` and observe relocation.

@quiz: Which taint effect can evict pods already running?
@option: NoSchedule
@correct: NoExecute
@option: PreferNoSchedule
