@title: Requests, Limits, and QoS: CPU and Memory
@icon: ⚖️
@description: Cgroups, eviction, Guaranteed vs Burstable, and node pressure.
@order: 4

# CPU and memory resources: what scheduler and kubelet enforce

**Requests** guide the **scheduler** (what fits on a node). **Limits** bound the **kubelet** via cgroups in the runtime. Without limits, one pod can **OOM** the node. This lesson explains **QoS classes**, **CPU throttling**, **eviction**, and practices.

@section: Requests and limits

* **requests:** minimum reservation expected; scheduler uses `allocatable`.
* **limits:** maximum allowed (hard for memory; CPU may throttle depending on config).

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

@section: QoS classes

* **Guaranteed:** requests == limits for all containers (most protected under pressure).
* **Burstable:** requests < limits or inconsistent definitions.
* **BestEffort:** no requests or limits (first evicted).

**OOMKilled:** container exceeds memory limit → restart.

@section: CPU: shares and CFS

CPU is usually **shared** by weights; **throttling** if the container exceeds its quota. Symptoms: high latency without OOM. Use **metrics** (cAdvisor) to see throttling.

@section: Node eviction

When the node runs out of memory or disk, the kubelet **evicts** pods in order based on QoS and priority. **BestEffort** first.

**ephemeral-storage** requests/limits matter for logs and writable layers.

@section: Horizontal Pod Autoscaler

**HPA** scales replicas based on CPU/memory/custom metrics; requires **metrics-server** or another adapter. **VPA** adjusts requests (watch interactions).

@section: Common mistakes

* `limits` without `requests` → unpredictable scheduling.
* Ignoring **ephemeral storage** for apps that write heavily to local disk.
* No **PodDisruptionBudget** when scaling aggressively with HPA.

@section: Suggested lab

1. Create pods with different QoS and generate memory pressure on a test node (carefully).
2. Observe eviction events and termination order.
3. Configure HPA with `metrics-server` for a test Deployment.

@quiz: Which QoS class is usually evicted first under memory pressure?
@option: Guaranteed
@correct: BestEffort
@option: Burstable always before BestEffort
