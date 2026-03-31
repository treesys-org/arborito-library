@title: ServiceAccounts, tokens y RBAC en workloads
@icon: 🎫
@description: Identidad de pods, TokenRequest API, roles y mínimo privilegio.
@order: 5

# ServiceAccounts: identidad dentro del clúster

Los **pods** usan una **ServiceAccount** (SA) para autenticarse con el **apiserver** (API calls, projected tokens) y para **RBAC** que limite qué recursos pueden leer. **default** SA existe por namespace pero no debes usarlo para apps sin revisar permisos.

@section: Tokens montados

Históricamente los tokens eran **secrets** largos montados automáticamente; ahora se **recomienda** **BoundServiceAccountToken** vía **projected volumes** (tokens de corta duración, audience).

**Deshabilita** automount si el pod no necesita llamar API (`automountServiceAccountToken: false`).

@section: RBAC

`Role` + `RoleBinding` (namespace) o `ClusterRole` + `ClusterRoleBinding` para permisos globales.

**Principio:** solo `get/list/watch` sobre lo necesario; evita `*` en recursos de producción.

**CI/CD:** SA dedicada para pipelines con permisos mínimos.

@section: Workload identity cloud

En GKE/EKS/AKS, **Workload Identity** / **IRSA** mapea SA a roles IAM de la nube para acceder a S3, etc., sin claves estáticas en pods.

**Audita** qué SA puede asumir qué rol IAM.

@section: Errores frecuentes

* Usar `cluster-admin` en tokens de aplicación.
* Olvidar rotación de tokens viejos en secretos.

@section: Laboratorio sugerido

1. Crea una SA y un Role que solo permita `get pods` en un namespace.
2. Monta la SA en un pod y prueba `kubectl` dentro (o client API) con permisos.
3. Verifica que sin RoleBinding el `403` aparece.

@quiz: ¿Para qué sirve principalmente `automountServiceAccountToken: false`?
@option: Desactivar RBAC
@correct: Evitar montar token de API en el pod si no necesita acceder al apiserver
@option: Aumentar réplicas
