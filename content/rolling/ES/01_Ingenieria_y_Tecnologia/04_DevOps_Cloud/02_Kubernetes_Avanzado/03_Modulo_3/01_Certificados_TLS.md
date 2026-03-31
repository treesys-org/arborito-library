@title: Certificados TLS en Kubernetes: PKI del clúster
@icon: 🔒
@description: Confianza entre componentes, rotación y CSR para usuarios y workloads.
@order: 1

# Certificados TLS: la base de confianza del clúster

Kubernetes usa **TLS** extensivamente: **apiserver** expone HTTPS, **kubelets** hablan con el apiserver, **etcd** cifra tráfico entre miembros. Entender **CA**, **certificados de cliente/servidor** y **rotación** es clave para operar clusters auto-gestionados y para **auditar** accesos.

@section: CAs del clúster

Típicamente existen CAs distintas o una jerarquía:

* **Cluster CA:** firma certificados del apiserver y a veces clientes internos.
* **etcd CA:** si está separada.

Los clientes (`kubectl`) confían en el **kubeconfig** que embebe el **certificate-authority-data** o apunta a archivo.

@section: Certificados de kubelet

El kubelet expone API local (métricas, exec) y se autentica al apiserver con certificados **rotados** por el proceso de **bootstrap** (CSR aprobados por controladores según política).

**Desconfiguración** de kubelet TLS → `Unauthorized` o nodos `NotReady`.

@section: Rotación

En kubeadm y muchos entornos hay **rotación automática** de certificados del plano de control; monitorea alertas de caducidad. Para **clusters largos**, caducidad sorpresa tumba el apiserver.

**cloud** gestionado reduce tu carga, pero aún debes rotar **certificados de ingress** y **webhooks**.

@section: Certificados para usuarios y mTLS

Puedes emitir certificados de cliente firmados por la CA del cluster para **usuarios** (con `kubectl` y `client-certificate`). Requiere **RBAC** asociado al **Common Name** u **Organization** según política.

**Alternativa moderna:** OIDC con IdP corporativo en lugar de certificados por persona.

@section: Admission webhooks y CRDs

**Validating/Mutating webhooks** deben servir TLS con **CA** confiable para el apiserver; suele desplegarse un **bundle** en el `WebhookConfiguration`.

**Secrets** tipo `kubernetes.io/tls` montan certificados en Ingress.

@section: Errores frecuentes

* Clock skew en nodos → TLS falla misteriosamente.
* Rotar CA sin actualizar **trust bundles** en webhooks.
* Certificados de **ingress** caducados visibles para usuarios finales.

@section: Laboratorio sugerido

1. Inspecciona `kubectl config view --raw` y localiza `certificate-authority-data`.
2. Lista secretos TLS en un namespace de aplicación y decodifica solo el `cert` (no expongas claves).
3. Lee la guía de rotación de certificados de tu herramienta (kubeadm, etc.).

@quiz: ¿Qué problema resuelve principalmente la CA del clúster en el kubeconfig del administrador?
@option: Comprimir logs
@correct: Verificar que el cliente kubectl confía en la identidad del apiserver
@option: Acelerar el scheduler
