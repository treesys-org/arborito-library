@title: Custom Schedulers and Multiple Profiles
@icon: 🗓️
@description: Scheduling profiles, extension, and when to use an alternative scheduler.
@order: 5

# Multiple schedulers and profiles: beyond the default

The default **kube-scheduler** assigns pods to nodes using **filters** and **scoring** (plugins). Sometimes you need distinct **profiles** (batch vs latency) or an **alternative** scheduler. This lesson introduces **Scheduling Profiles**, `schedulerName`, and operational limits.

@section: Default scheduler

The scheduler considers:

* **Resource requests**, **taints/tolerations**, **node affinity**, **pod affinity/anti-affinity**, **topology spread**, **priority**.
* Plugins enabled via **KubeSchedulerConfiguration**.

**Extension:** many cases are solved via **configuration** instead of a custom binary.

@section: Scheduling profiles

In recent versions you can define **multiple profiles** in one scheduler binary with different `schedulerName` on pods (depending on cluster configuration).

**Use case:** batch workloads with different plugins and priorities.

@section: Alternative scheduler

You can deploy another **kube-scheduler** with its own configuration and reference it:

```yaml
spec:
  schedulerName: my-custom-scheduler
```

Requires **RBAC**, **leader election**, and version **compatibility**.

**Not** trivial: most teams avoid custom schedulers unless requirements are strong.

@section: When you might not need one

Before a new scheduler, evaluate:

* **PriorityClass** and **preemption**.
* **Topology spread constraints**.
* **Taints** and node pools.

@section: Debugging

* Pod events (`FailedScheduling`) with reasons.
* Scheduler logs with `--v` to see scoring.

@section: Common mistakes

* `schedulerName` pointing to a non-existent scheduler → pods stuck `Pending`.
* Two schedulers with misconfigured **leader election**.

@section: Suggested lab

1. Inspect default scheduler configuration in `kube-system` (depending on access).
2. Create a pod with `priorityClassName` and observe preemption in a test environment.
3. Read **scheduling profiles** docs for your version and summarize when to use them.

@quiz: Which pod field selects which scheduler places it?
@option: priorityClassName
@correct: schedulerName
@option: nodeName
