@title: Static Pods y el kubelet
@icon: 📌
@description: Manifiestos en el filesystem del nodo, uso en bootstrapping y diferencias con DaemonSet.
@order: 4

# Static Pods: control local del kubelet

Un **static pod** es un pod definido por un archivo de manifiesto en el **filesystem** del nodo y gestionado **solo** por el **kubelet**, no por el apiserver (aunque el apiserver puede reflejarlo como espejo). Se usa para **bootstrapping** del control plane (manifests en `/etc/kubernetes/manifests` con kubeadm) y casos especiales.

@section: Cómo funciona

Colocas un YAML en el directorio observado por el kubelet (`staticPodPath`). El kubelet crea el pod localmente y **reinicia** si el proceso cae, según políticas normales.

El apiserver puede mostrar un **mirror pod** con sufijo `-<nodename>` para visibilidad.

@section: Diferencias frente a DaemonSet

| Aspecto | Static Pod | DaemonSet |
|--------|------------|-----------|
| Controlado por | kubelet en ese nodo | control plane (controller) |
| Uso típico | Control plane, bootstrapping | Agente por nodo (CNI helper, logs) |
| Visibilidad | Limitada al nodo | API estándar |

**DaemonSet** es preferible para agentes de cluster administrados vía API; **static pod** cuando el apiserver aún no existe o para acoplamiento fuerte al nodo.

@section: Limitaciones

* No uses static pods para aplicaciones de negocio salvo razón muy fuerte.
* Gestión por **SSH** a cada nodo no escala; difícil GitOps puro sin herramientas extra.

@section: Seguridad

El directorio de manifiestos es **altamente privilegiado**: restringe permisos del sistema de archivos y quién puede escribir. Un atacante con acceso puede elevar privilegios.

@section: Depuración

`crictl` / `docker` en el nodo para inspeccionar contenedores; logs del kubelet si el pod no aparece. Verifica rutas y sintaxis YAML (un error puede impedir el arranque del componente).

@section: Errores frecuentes

* Confundir mirror pod con pod «normal» y borrarlo desde API sin efecto duradero.
* Mezclar static pods con el mismo nombre en varios nodos sin coordinación.

@section: Laboratorio sugerido

1. En un nodo de prueba (no producción), coloca un manifiesto mínimo de static pod y observa su aparición con `kubectl get pods -A` (según configuración).
2. Elimina el archivo y verifica que el pod desaparece.
3. Documenta cuándo tu organización permite static pods y cuándo exige DaemonSet.

@quiz: ¿Quién es la autoridad principal que gestiona un static pod?
@option: El Deployment controller
@correct: El kubelet en el nodo donde está el archivo de manifiesto
@option: Solo el etcd
