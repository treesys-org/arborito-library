
@title: Deployments and rolling updates
@icon: 🚀
@description: ReplicaSet, RollingUpdate strategy, rollback, and health probes.
@order: 1

# Deployments

A **Deployment** declares **stateless** apps that should run N replicas. It owns a **ReplicaSet** that creates **Pods** from a pod template. When you change the image or template, the Deployment performs a **controlled rollout**.

@section: ReplicaSet

A ReplicaSet ensures a desired number of **matching** Pods exist. If a node fails, Pods are rescheduled. Deployments add **revision history** and **deployment strategy** on top.

@section: RollingUpdate

Default `strategy.type: RollingUpdate` with `maxUnavailable` and `maxSurge` (percentages allowed). New Pods are added and old ones removed according to those limits.

```yaml
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
```

@section: Rollback

Each change keeps an old **ReplicaSet** (subject to `revisionHistoryLimit`):

```bash
kubectl rollout history deployment/my-app
kubectl rollout undo deployment/my-app
kubectl rollout status deployment/my-app
```

@section: Probes

*   **livenessProbe:** restart container if failing.
*   **readinessProbe:** remove Pod from Service endpoints until healthy.
*   **startupProbe:** grace period for slow starts before liveness applies.

Use `httpGet`, `tcpSocket`, or `exec` with realistic `initialDelaySeconds` / `periodSeconds`.

@quiz: Which object directly owns Pods created from a Deployment template?
@option: The Deployment with no intermediary
@correct: The ReplicaSet managed by the Deployment
@option: The associated Service only
