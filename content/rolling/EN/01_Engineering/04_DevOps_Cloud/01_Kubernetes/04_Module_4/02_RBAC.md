
@title: RBAC in Kubernetes
@icon: 🔑
@description: Roles, ClusterRoles, Bindings, ServiceAccounts.
@order: 2

# RBAC

**Role-based access control** limits which identities can perform which **verbs** on which **resources** after authentication (certs, tokens, OIDC).

@section: Role vs ClusterRole

*   **Role + RoleBinding:** **namespace** scoped.
*   **ClusterRole + ClusterRoleBinding:** cluster-wide, or referenced from a RoleBinding to grant cluster permissions to a subject in one namespace.

@section: ServiceAccount

Namespaces have default SAs; Pods set **serviceAccountName**. With short-lived **bound tokens** (1.22+), rotation is automatic. On cloud, **workload identity** maps SAs to IAM roles.

@section: Least privilege

Avoid handing **cluster-admin** to CI jobs. Watch privilege escalation verbs (`escalate`, `bind` on roles).

@section: kubectl auth

```bash
kubectl auth can-i create deployments --namespace team-a
kubectl auth can-i --list --as=system:serviceaccount:ns:sa
```

@quiz: Which object binds subjects (User/Group/SA) to a Role in a namespace?
@option: ClusterRoleBinding only
@correct: RoleBinding
@option: kube-apiserver without objects
