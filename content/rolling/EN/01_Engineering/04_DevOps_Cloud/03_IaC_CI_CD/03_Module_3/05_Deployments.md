@title: Deployment Strategies: Rolling, Blue-Green, and Canary
@icon: 🚀
@description: Risk reduction, rollback, and fit with Kubernetes and Istio.
@order: 5

# Deployment strategies: speed with controlled risk

Deploying a new version is not only “replace binaries”: you choose **how** you introduce traffic and **how** you roll back if something fails. This lesson summarizes classic patterns and how they fit with load balancers, Kubernetes, and service mesh.

@section: Rolling update

**Rolling update** replaces instances gradually **N at a time**. Advantage: without duplicating full capacity. Disadvantage: during the window **two versions** coexist; clients may see mixed behavior if APIs are not backward compatible.

In Kubernetes: `Deployment` with `maxSurge` and `maxUnavailable` controls the pace.

@section: Blue-green

**Blue-green** maintains **two full environments** (blue current, green new). After validating green, you **switch traffic** in one step (DNS, load balancer, routing table). If it fails, revert to blue.

Cost: **double capacity** temporarily. Ideal when you need strong validation before cutover.

@section: Canary

**Canary** sends a **small percentage** of users or requests to the new version and watches metrics (errors, latency). If healthy, increase the percentage to 100%.

Implementation: weighted load balancers, **Istio/Linkerd** traffic splits, **feature flags** combined with metrics.

@section: Recreate

**Recreate** tears down the old before bringing up the new. There is **downtime**; only acceptable during maintenance windows or batch jobs.

@section: Rollback

**Rollback** is not magic: you need:

* Addressable artifact versions (image tags, releases).
* **Compatible** database migrations (expand/contract) or reversible scripts.
* A runbook listing which metrics to check before declaring success.

In Kubernetes: `kubectl rollout undo` for `Deployment` if the previous revision is still available.

@section: Databases and deployments

Schema changes break deployments if old and new app versions do not share the same contract. Patterns:

* **Expand:** add nullable columns first.
* **Contract:** remove after all versions use the new field.

@section: Observability during deployment

Define **SLIs** (p95 latency, error rate) and **SLOs**; alert if the canary worsens vs baseline. **Structured logs** with `version` or `deployment_id` help correlate.

@section: Common mistakes

* Friday late deploys without rollback ownership.
* Assuming blue-green removes the need for API compatibility testing.
* Ignoring data migration in the deployment plan.

@section: Suggested lab

1. With a test Kubernetes `Deployment`, tune `maxUnavailable` and observe update duration.
2. Simulate a failure in the new version and practice `rollout undo`.
3. Write a one-page runbook: failure signals, who runs rollback, how to verify recovery.

@quiz: What is the main disadvantage of rolling update compared to blue-green?
@option: It always duplicates infrastructure
@correct: Two versions coexist during the window; inconsistency if the API is not compatible
@option: It does not allow rollback
