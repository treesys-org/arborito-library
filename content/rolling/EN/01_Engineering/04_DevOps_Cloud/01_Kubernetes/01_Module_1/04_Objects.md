
@title: API objects
@icon: 📋
@description: apiVersion, kind, metadata, spec, status, labels, selectors.
@order: 4

# Kubernetes API objects

Every persisted resource has **apiVersion**, **kind**, **metadata**, **spec** (desired), and **status** (observed, filled by the system).

@section: Minimal YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example
  namespace: default
  labels:
    app: demo
spec:
  containers:
    - name: app
      image: nginx:1.27-alpine
```

`metadata.name` + `namespace` identify namespaced objects. Some types are **cluster-scoped** (`Node`, `ClusterRole`, `PersistentVolume`…).

@section: Labels and selectors

**Labels** are indexed key/value pairs used by **selectors** (Deployments → Pods, Services → Pods). **Annotations** hold non-selectable metadata (build IDs, contacts, policies).

@section: Finalizers and ownerReferences

**Finalizers** delay deletion until controllers finish cleanup. **ownerReferences** link dependents (a Pod owned by a ReplicaSet) for **cascading** deletes.

@section: Discover versions

```bash
kubectl api-resources
kubectl explain deployment --api-version=apps/v1
```

@quiz: Which field holds observed state updated by controllers?
@option: spec
@correct: status
@option: metadata.uid only
