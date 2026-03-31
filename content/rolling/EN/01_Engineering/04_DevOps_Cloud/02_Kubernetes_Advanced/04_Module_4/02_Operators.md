@title: Operators: Control Loops and the Kubernetes Pattern
@icon: 🤖
@description: Reconcile loops, SDKs, and operating stateful systems.
@order: 2

# Operators: automate operations with code

An **Operator** watches **CRDs** and runs **reconcile**: drive external state (databases, clusters) toward the **desired spec**. **etcd**, **Kafka**, **Postgres** on Kubernetes often use mature Operators. This lesson describes the **pattern**, **SDKs**, and risks.

@section: Reconcile loop

1. Watch resource events and dependencies.
2. Compare desired vs actual state.
3. Act (create StatefulSets, Secrets, backups).
4. Update **status** and requeue on failure (with backoff).

**Idempotence:** retries must be safe.

@section: Frameworks

* **Operator SDK** (Go, Ansible, Helm).
* **Kubebuilder** scaffolds Go projects.
* **Kopf** (Python) for prototypes.

**Helm Operator** wraps charts; useful if Helm already exists.

@section: Upgrades and migrations

Operators must handle **version upgrades** of the managed system without data loss. Read Operator **release notes** (CRD changes, renamed fields).

@section: Risks

* Operators with broad **cluster-wide** RBAC.
* Bugs that delete PVCs or Secrets.

**Vendor lock-in** is partial: your data is still yours, but the CRD is a contract.

@section: Observability

Expose Operator **metrics** (reconcile duration, errors). **Structured logs** with `namespace/name` of the resource.

@section: Suggested lab

1. Install a demo Operator in a test cluster (e.g. minimal prometheus-operator).
2. Watch operator pods and created CRs.
3. Simulate spec errors and review events/status.

@quiz: What does an Operator’s reconcile function mainly do?
@option: Only create random pods
@correct: Align real system state with the spec declared in the custom resource
@option: Format YAML
