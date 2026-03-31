@title: TLS Certificates: Cluster PKI
@icon: 🔒
@description: Trust between components, rotation, and CSRs for users and workloads.
@order: 1

# TLS certificates: trust foundations in Kubernetes

Kubernetes uses **TLS** extensively: the **apiserver** serves HTTPS, **kubelets** talk to the apiserver, **etcd** encrypts member traffic. Understanding **CAs**, **client/server certificates**, and **rotation** is essential for self-managed clusters and for **auditing** access.

@section: Cluster CAs

Typically there are distinct CAs or a hierarchy:

* **Cluster CA:** signs apiserver certs and sometimes internal clients.
* **etcd CA:** if separated.

Clients (`kubectl`) trust the **kubeconfig** embedding **certificate-authority-data** or pointing to a file.

@section: Kubelet certificates

The kubelet exposes a local API (metrics, exec) and authenticates to the apiserver with certificates **rotated** via **bootstrap** (CSRs approved by controllers per policy).

**Misconfigured** kubelet TLS → `Unauthorized` or `NotReady` nodes.

@section: Rotation

In kubeadm and many environments **automatic rotation** exists for control plane certs; monitor expiry alerts. For **long-lived** clusters, surprise expiry can take down the apiserver.

**Managed cloud** reduces your load, but you still rotate **Ingress** and **webhook** certificates.

@section: Certificates for users and mTLS

You can issue client certificates signed by the cluster CA for **users** (`kubectl` with `client-certificate`). Requires **RBAC** tied to **Common Name** or **Organization** per policy.

**Modern alternative:** OIDC with a corporate IdP instead of per-user certificates.

@section: Admission webhooks and CRDs

**Validating/Mutating webhooks** must serve TLS trusted by the apiserver; usually a **caBundle** in `WebhookConfiguration`.

**Secrets** of type `kubernetes.io/tls` mount certs for Ingress.

@section: Common mistakes

* Clock skew on nodes → mysterious TLS failures.
* Rotating CA without updating webhook **trust bundles**.
* **Ingress** certificates expired for end users.

@section: Suggested lab

1. Inspect `kubectl config view --raw` and locate `certificate-authority-data`.
2. List TLS secrets in an app namespace and decode only `cert` (do not expose keys).
3. Read your tool’s certificate rotation guide (kubeadm, etc.).

@quiz: What problem does the cluster CA in the admin kubeconfig mainly solve?
@option: Compressing logs
@correct: Ensuring the kubectl client trusts the apiserver identity
@option: Speeding up the scheduler
