@title: CRDs: Extending the Kubernetes API
@icon: 🧩
@description: Defining custom resources, versioning, and conversions.
@order: 1

# CustomResourceDefinitions: your own object type

A **CRD** extends the apiserver with new kinds (`apiVersion`/`kind`) stored like any API object. Operators and platforms (Istio, cert-manager, Prometheus Operator) rely on CRDs. This lesson covers **OpenAPI schema**, **versioning**, **conversions**, and **pruning**.

@section: Defining a CRD

A `CustomResourceDefinition` manifest declares:

* **group** (e.g. `example.com`)
* **names** (plural, singular, kind, shortNames)
* **scope** Namespaced or Cluster
* **versions** with **schema** JSON/OpenAPI

**Server-side validation** rejects invalid objects before persisting to etcd.

@section: Versioning

Multiple **versions** (`v1alpha1`, `v1`) can coexist with **served** and **storage** (one stored version in etcd). **Conversion webhooks** translate between versions.

**Deprecation:** plan migrations for clients and GitOps manifests.

@section: Subresources

The `status` subresource separates **spec** (user) from **status** (controller). RBAC differs for `update` vs `update/status`.

@section: Finalizers

**Finalizers** block deletion until controllers clean up external resources. Poorly managed → objects stuck terminating.

@section: Common mistakes

* Overly lax schema → technical debt.
* CRDs installed with broad permissions in multi-tenant clusters.

@section: Suggested lab

1. Apply an example CRD from the official docs (trivial resource).
2. Create a custom object and observe `kubectl get`.
3. Delete the CRD and understand how instances disappear (care in prod).

@quiz: What does a CustomResourceDefinition primarily define?
@option: A new physical node
@correct: A new resource type in the Kubernetes API with schema and versions
@option: A TLS secret
