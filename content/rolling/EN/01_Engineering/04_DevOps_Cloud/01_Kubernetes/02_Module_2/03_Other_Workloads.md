
@title: StatefulSet, DaemonSet, Jobs, CronJobs
@icon: 🧩
@description: Identity-aware workloads, one pod per node, and batch execution.
@order: 3

# Other workload controllers

Not everything fits a Deployment. Kubernetes provides primitives for **state**, **per-node topology**, and **batch** execution.

@section: StatefulSet

Pods with **stable ordinals** (`app-0`, `app-1`), headless DNS, and often **per-replica PVCs**. Common for clustered data systems. Creation/deletion ordering is strict; scaling/upgrades need more care than Deployments.

@section: DaemonSet

Ensures **one Pod per node** (or a subset via node selectors). Typical for log agents, node monitoring, some CNI components, classic **kube-proxy** models.

@section: Job and CronJob

A **Job** runs a Pod until **successful completion** (`backoffLimit`, `ttlSecondsAfterFinished`). **CronJob** schedules Jobs with cron syntax; watch overlaps (`concurrencyPolicy`) and history limits.

@section: HorizontalPodAutoscaler

**HPA** scales replicas based on CPU, memory, or custom metrics (Prometheus adapter). Requires **metrics-server** or another metrics pipeline. It is not a substitute for sensible baseline sizing.

@quiz: Which workload commonly provides stable identity and per-replica storage?
@option: Deployment with replicas: 1
@correct: StatefulSet
@option: A Job only
