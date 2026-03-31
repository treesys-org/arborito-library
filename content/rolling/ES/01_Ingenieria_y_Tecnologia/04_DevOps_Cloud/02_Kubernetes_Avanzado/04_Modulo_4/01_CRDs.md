@title: CRDs: extender la API de Kubernetes
@icon: 🧩
@description: Definición de recursos propios, versionado y conversiones.
@order: 1

# CustomResourceDefinitions: tu propio tipo de objeto

Un **CRD** extiende el apiserver con nuevos tipos (`apiVersion`/`kind`) gestionados como objetos de la API. Operadores y plataformas (Istio, cert-manager, Prometheus Operator) se apoyan en CRDs. Esta lección cubre **esquema OpenAPI**, **versionado**, **conversiones** y **pruning**.

@section: Definir un CRD

Un manifiesto `CustomResourceDefinition` declara:

* **group** (e.g. `example.com`)
* **names** (plural, singular, kind, shortNames)
* **scope** Namespaced o Cluster
* **versions** con **schema** JSON/OpenAPI

**Validation** en servidor evita objetos inválidos antes de persistir en etcd.

@section: Versionado

Múltiples **versions** (`v1alpha1`, `v1`) pueden coexistir con **served** y **storage** (una versión almacenada en etcd). Las **conversion webhooks** traducen entre versiones.

**Deprecación:** planifica migraciones de clientes y manifiestos GitOps.

@section: Subresources

`status` subresource separa **spec** (usuario) de **status** (controlador). RBAC distinto para `update` vs `update/status`.

@section: Finalizers

**Finalizers** impiden borrado hasta que controladores limpien recursos externos. Mal gestionados → objetos atascados en terminación.

@section: Errores frecuentes

* Esquema demasiado laxo → deuda técnica.
* CRDs instaladas con permisos amplios en clusters multi-tenant.

@section: Laboratorio sugerido

1. Aplica un CRD de ejemplo de la documentación oficial (recurso trivial).
2. Crea un objeto custom y observa `kubectl get`.
3. Borra el CRD y entiende cómo desaparecen instancias (cuidado en prod).

@quiz: ¿Qué define principalmente un CustomResourceDefinition?
@option: Un nuevo nodo físico
@correct: Un nuevo tipo de recurso en la API de Kubernetes con esquema y versiones
@option: Un secreto TLS
