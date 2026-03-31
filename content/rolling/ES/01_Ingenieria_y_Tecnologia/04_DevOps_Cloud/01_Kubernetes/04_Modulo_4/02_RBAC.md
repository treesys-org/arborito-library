
@title: RBAC en Kubernetes
@icon: 🔑
@description: Roles, ClusterRoles, Bindings y ServiceAccounts.
@order: 2

# RBAC

El **control de acceso basado en roles** limita qué identidades pueden invocar qué **verbos** (`get`, `list`, `watch`, `create`, `patch`, `delete`…) sobre qué **recursos**. Se evalúa tras autenticación (cert, token, OIDC).

@section: Role vs ClusterRole

*   **Role + RoleBinding:** ámbito de **namespace**.
*   **ClusterRole + ClusterRoleBinding:** todo el clúster o referenciado desde un RoleBinding para dar permisos cluster a un sujeto en un NS (patrón admin de namespace).

@section: ServiceAccount

Cada namespace tiene SAs por defecto; los Pods usan **serviceAccountName**. Con **BoundServiceAccountToken** (v1.22+), los tokens son cortos y rotativos. En cloud, **IRSA/GKE Workload Identity** proyectan identidad hacia IAM.

@section: Principio de mínimo privilegio

Evita `cluster-admin` en CI. Usa **roles dedicados** por controlador o pipeline. Revisa **escalation paths** (`escalate`, `bind` sobre roles).

@section: kubectl auth

```bash
kubectl auth can-i create deployments --namespace equipo-a
kubectl auth can-i --list --as=system:serviceaccount:ns:sa
```

@quiz: ¿Qué par enlaza sujetos (User/Group/SA) con un Role en un namespace?
@option: ClusterRoleBinding solamente
@correct: RoleBinding
@option: Solo el kube-apiserver sin objetos
