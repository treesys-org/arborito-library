@title: kubeconfig: Contexts, Clusters, and Credentials
@icon: ⚙️
@description: Multiple environments, exec plugins, and security practices.
@order: 2

# kubeconfig: manage access without losing track

The **kubeconfig** file (default `~/.kube/config`) describes **clusters**, **users** (credentials), and **contexts** that bind them with a default **namespace**. Mastering **config merges**, **exec plugins**, and **least privilege** avoids “I applied to production thinking it was dev.”

@section: Structure

* **clusters:** apiserver endpoint + CA.
* **users:** cert/key, token, or `exec` to fetch dynamic tokens (OIDC, cloud).
* **contexts:** cluster + user + default `namespace`.
* **current-context:** what `kubectl` uses without extra flags.

@section: Multiple environments

Use **explicit names** (`prod-admin`, `dev-readonly`). Shell **aliases** (`kdev`, `kprod`) reduce mistakes.

**KUBECONFIG** can list multiple files separated by `:`; kubectl merges them.

@section: Exec plugins

Cloud providers and OIDC often use **exec plugins** that fetch **short-lived** tokens. **Do not** store long-lived secrets in plaintext when alternatives exist.

**Expiry:** tokens expire; CI must refresh credentials.

@section: RBAC and least privilege

Even with the right kubeconfig, **Roles/ClusterRoles** limit what you can do. For CI, dedicated **ServiceAccounts** with minimal permissions.

@section: Impersonation

`kubectl --as` and `--as-group` help test policies; do not abuse in production without auditing.

@section: Common mistakes

* Same **context** name pointing at different clusters after copying configs.
* `insecure-skip-tls-verify: true` in production.
* Sharing admin kubeconfig via chat.

@section: Suggested lab

1. Create two test contexts (or use minikube + kind) and switch with `kubectl config use-context`.
2. Export manifests with explicit `--context`.
3. (Optional) Configure your shell prompt to show the current context.

@quiz: What does a kubeconfig “context” bind together?
@option: Only the namespace
@correct: A cluster, an authentication user, and optionally the default namespace
@option: Only the TLS certificate
