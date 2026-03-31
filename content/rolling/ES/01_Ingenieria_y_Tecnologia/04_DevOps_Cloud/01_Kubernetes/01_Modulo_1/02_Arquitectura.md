
@title: Arquitectura del clúster K8s
@icon: 🏛️
@description: Plano de control, nodos trabajadores, CRI, CNI y CSI en contexto.
@order: 2

# Arquitectura de Kubernetes

Un clúster se divide en el **plano de control (control plane)** y los **nodos trabajadores (worker nodes)**. Toda la configuración deseada pasa por la **API**; los componentes observan el estado y lo convergen.

@section: Plano de control

*   **kube-apiserver:** única entrada HTTP/gRPC autenticada al API; valida y persiste el estado deseado en **etcd**.
*   **etcd:** almacén clave-valor consistente y distribuido; guarda todos los objetos del clúster.
*   **kube-scheduler:** asigna Pods **sin nodo** a un nodo viable según recursos, afinidad, taints, prioridad, etc.
*   **kube-controller-manager:** ejecuta **controladores** (por ejemplo ReplicaSet, Deployment, Node) que comparan estado deseado vs real y emiten acciones.
*   **cloud-controller-manager** (opcional en cloud): integra con el proveedor (LB, rutas, volúmenes gestionados).

@section: Nodo trabajador

*   **kubelet:** agente que registra el nodo, recibe asignaciones de Pods y habla con el **runtime** (CRI) para crear contenedores; ejecuta **probes** y reporta estado.
*   **kube-proxy** (o reemplazo según CNI): mantiene reglas para **Services** (iptables/nftables/IPVS o dataplane del CNI).
*   **Container runtime:** containerd, CRI-O, etc.

@section: CNI, CSI y CRI

*   **CRI** — Container Runtime Interface: cómo el kubelet arranca contenedores.
*   **CNI** — Container Network Interface: plugins que dan IP a Pods y conectividad multi-nodo.
*   **CSI** — Container Storage Interface: aprovisionamiento y montaje de volúmenes externos.

@section: Flujo típico

1. Aplicas un manifiesto al **apiserver**.
2. **etcd** guarda el objeto; el **Deployment controller** crea/ajusta **ReplicaSets** y **Pods**.
3. El **scheduler** elige nodo; el **kubelet** del nodo arranca contenedores.
4. **CNI** asigna red; **kube-proxy/CNI** enruta tráfico hacia **ClusterIP/NodePort**.

@quiz: ¿Dónde persiste el estado deseado de los objetos del clúster?
@option: Solo en ficheros del kubelet
@correct: En etcd (accesado vía kube-apiserver)
@option: En la memoria del scheduler únicamente
