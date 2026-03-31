@title: ServiceAccounts, Tokens, and Workload RBAC
@icon: 🎫
@description: Pod identity, TokenRequest API, roles, and least privilege.
@order: 5

# ServiceAccounts: identity inside the cluster

**Pods** use a **ServiceAccount** (SA) to authenticate to the **apiserver** (API calls, projected tokens) and for **RBAC** that limits which resources they can read. A **default** SA exists per namespace but you should not use it for apps without reviewing permissions.

@section: Mounted tokens

Historically tokens were long **secrets** auto-mounted; **BoundServiceAccountToken** via **projected volumes** (short-lived tokens, audience) is now recommended.

**Disable** automount if the pod does not need API access (`automountServiceAccountToken: false`).

@section: RBAC

`Role` + `RoleBinding` (namespace) or `ClusterRole` + `ClusterRoleBinding` for cluster-wide permissions.

**Principle:** only `get/list/watch` what is necessary; avoid `*` on production resources.

**CI/CD:** dedicated SA for pipelines with minimal permissions.

@section: Cloud workload identity

On GKE/EKS/AKS, **Workload Identity** / **IRSA** maps SAs to cloud IAM roles for S3 access without static keys in pods.

**Audit** which SA can assume which IAM role.

@section: Common mistakes

* Using `cluster-admin` for application tokens.
* Forgetting to rotate old tokens stored in Secrets.

@section: Suggested lab

1. Create an SA and Role that only allows `get pods` in a namespace.
2. Run a pod with that SA and test API permissions.
3. Verify `403` without RoleBinding.

@quiz: What is `automountServiceAccountToken: false` mainly for?
@option: Disabling RBAC
@correct: Avoid mounting an API token if the pod does not need apiserver access
@option: Increasing replicas
