
@title: Imperative vs declarative
@icon: ⚖️
@description: kubectl create/run vs apply; declarative management and GitOps.
@order: 5

# Management styles

You can create resources **imperatively** (`kubectl run`, `kubectl create`) or **declaratively** (YAML + `kubectl apply`). Production favors **declarative** manifests in Git.

@section: Quick imperative

Useful for experiments:

```bash
kubectl create deployment demo --image=nginx:1.27-alpine --replicas=2
kubectl expose deployment demo --port=80 --type=ClusterIP
```

Risk: hard to **reproduce** and audit. Not a substitute for repo-backed manifests.

@section: Declarative apply

`kubectl apply -f manifest.yaml` is **idempotent** (server-side apply tracks `managedFields`). Patterns: one file/dir per app; **Kustomize** or **Helm** for variants.

@section: Diff and validation

```bash
kubectl diff -f .
kubectl apply --dry-run=server -f deployment.yaml
```

`--dry-run=server` validates against admission and API without persisting.

@section: GitOps

Tools like **Argo CD** or **Flux** reconcile cluster state from Git. Manual `kubectl edit` becomes **drift** to fix.

@quiz: Which command is commonly used for idempotent manifest application?
@option: kubectl create only
@correct: kubectl apply -f …
@option: kubectl run exclusively
