
@title: ConfigMaps and Secrets
@icon: 🔐
@description: Non-secret config, sensitive data, mounting patterns, and hygiene.
@order: 4

# Configuration and secrets

**ConfigMap** holds **non-secret** configuration. **Secret** holds sensitive material; **encryption at rest** protects etcd, but on the node data still appears as files/env—**not** a replacement for an enterprise vault.

@section: Injecting into Pods

*   **env** / **envFrom** for variables.
*   **volumes** of type `configMap` or `secret` mounted as files (`defaultMode`).

`Opaque` Secrets are base64 in YAML for transport only—not encryption.

@section: Updates

Volume-mounted ConfigMaps **refresh** file contents without Pod restart (after a short delay). Environment variables **do not** refresh without restart.

@section: Good practices

*   Tight **RBAC** on `secrets`.
*   **External Secrets Operator** or cloud KMS integration for rotation.
*   Never commit real Secrets to Git; **Sealed Secrets** or **SOPS** if Git storage is required.

@section: kubectl

```bash
kubectl create configmap app-config --from-file=app.properties
kubectl create secret generic db --from-literal=password=...
```

@quiz: If a ConfigMap is mounted as a volume and you update the ConfigMap, what happens to files in the Pod?
@option: They never change until the image changes
@correct: Files on the volume typically update without recreating the Pod (with delay)
@option: Kubernetes always reboots the entire node
