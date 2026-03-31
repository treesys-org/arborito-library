
@title: Helm — charts and releases
@icon: ⎈
@description: Go templates, values, upgrade/rollback, dependencies.
@order: 1

# Helm

**Helm** packages Kubernetes manifests into versioned **charts**: YAML with **Go templates**, external **values**, and **release** lifecycle metadata stored in-cluster (Secrets/ConfigMaps depending on Helm version).

@section: Chart layout

```
Chart.yaml
values.yaml
templates/
```

`helm install my-release ./chart -f prod.yaml` renders objects with release-prefixed names.

@section: Upgrade and rollback

```bash
helm upgrade my-release ./chart --install
helm history my-release
helm rollback my-release 2
```

Use **`--atomic`** to auto-rollback failed upgrades.

@section: Hooks and tests

**Hooks** (`pre-install`, `post-upgrade`, …) run Jobs at lifecycle points. **`helm test`** runs chart-defined test Pods.

@section: OCI registries

Charts can live in **OCI** registries alongside images, complementing classic `helm repo` workflows.

@section: When not to use Helm

For very small apps, **Kustomize** or plain YAML + GitOps may suffice. Helm adds **templating** and **values** complexity—including secret handling discipline.

@quiz: Which file usually holds default tunable values for a chart?
@option: templates/values.toml
@correct: values.yaml at the chart root
@option: Chart.lock only
