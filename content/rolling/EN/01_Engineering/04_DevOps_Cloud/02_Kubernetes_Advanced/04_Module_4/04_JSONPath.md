@title: JSONPath and Field Queries with kubectl
@icon: 🔎
@description: `-o jsonpath` queries, ranges, and quick debugging.
@order: 4

# JSONPath: cut through noisy `kubectl get` output

**JSONPath** is a small language to select fields from the apiserver JSON response. `kubectl get -o jsonpath='{...}'` is essential in scripts and **debugging** when `-o wide` is not enough.

@section: Basic syntax

```bash
kubectl get pods -n kube-system -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'
```

* `{.field}` navigates the object.
* `[*]` iterates lists.
* `range` / `end` for loops.
* `{"\n"}` prints newlines (escape quotes in your shell).

@section: Typical uses

* Extract pod **IPs** (`status.podIP`).
* List **nodes** with specific labels.
* **Health check** scripting in CI.

@section: jq as an alternative

`kubectl get -o json | jq` is more readable for complex pipelines; JSONPath is enough for simple cuts without extra dependencies.

@section: Common mistakes

* `nil` fields → empty output without a clear error.
* Confusing `metadata.name` vs `metadata.generateName`.

@section: Suggested lab

1. Get all `podIP` values for a Deployment with jsonpath.
2. Combine with `kubectl get node -o jsonpath` to print `InternalIP`.
3. Write a script that fails if any pod is not `Ready=True`.

@quiz: What is jsonpath in kubectl mainly used for?
@option: Encrypting secrets
@correct: Extracting specific fields from apiserver JSON for scripts and debugging
@option: Applying manifests
