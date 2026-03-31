@title: Admission Controllers and Webhooks
@icon: 🚧
@description: Validating and mutating hooks, execution order, and availability.
@order: 3

# Admission: validate and mutate before persistence

**Admission controllers** intercept requests to the apiserver **after** authentication/authorization and **before** writing to etcd. External **webhooks** (validating/mutating) enable custom policies (OPA, Kyverno, Istio sidecar injection). This lesson covers **order**, **fail-open vs fail-closed**, and **high availability**.

@section: Mutating vs validating

* **Mutating:** may change the object (inject sidecar, labels).
* **Validating:** only approves or rejects.

**Order:** mutating first, then validating (simplified; check your version).

@section: WebhookConfiguration

`ValidatingWebhookConfiguration` / `MutatingWebhookConfiguration` define:

* **clientConfig** (URL or service)
* **rules** (resources/verbs)
* **failurePolicy** `Fail` or `Ignore`
* **timeoutSeconds**
* **sideEffects** / **matchPolicy**

The apiserver must **trust** the webhook service TLS CA bundle.

@section: Availability

If the webhook is down and `failurePolicy: Fail`, **all** create/update operations can block → major incident.

**Strategies:** multiple replicas, **PodDisruptionBudget**, reasonable **timeouts**, **FailOpen** only if you accept compliance risk.

@section: Common policies

* Require minimum `resources`.
* Block `privileged` pods.
* Verify image signatures.

@section: Common mistakes

* Slow webhooks → global API latency.
* TLS certs not rotated on the webhook service.

@section: Suggested lab

1. List webhooks in your cluster (`kubectl get validatingwebhookconfiguration`).
2. Inspect an existing Kyverno/OPA policy and its webhook translation.
3. Simulate webhook downtime in staging and observe impact.

@quiz: What is the main difference between mutating and validating admission?
@option: Only the name
@correct: Mutating puede modificar el objeto; validating solo acepta o rechaza
@option: Validating runs before authentication

Wait I made an error - the quiz should be in English for EN file. Let me fix that file.
</think>
Corrigiendo el quiz en inglés en `03_Admission_Controllers.md`.

<｜tool▁calls▁begin｜><｜tool▁call▁begin｜>
StrReplace