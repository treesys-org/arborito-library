
@title: Services y descubrimiento
@icon: 🔌
@description: ClusterIP, NodePort, LoadBalancer, Endpoints y headless.
@order: 2

# Services

Los Pods son **efímeros** y cambian de IP. Un **Service** ofrece una **IP virtual estable** (**ClusterIP**) y un **DNS** (`<svc>.<ns>.svc.cluster.local`) que balancea hacia un conjunto de Pods vía **selector** o **Endpoints** manuales.

@section: ClusterIP

Tipo por defecto: tráfico solo **dentro** del clúster. kube-proxy (o el dataplane del CNI) programa reglas para DNAT hacia IPs de Pod.

@section: NodePort

Abre un puerto en **cada nodo** hacia el Service. Útil en laboratorio; en producción suele ir delante un **LoadBalancer** o Ingress.

@section: LoadBalancer

Pide al **cloud controller** una VIP externa. En bare metal necesitas implementaciones tipo MetalLB o kube-vip.

@section: Service sin selector y headless

*   Sin selector: defines **Endpoints** o **EndpointSlice** a mano (tráfico a IPs fuera del clúster o legado).
*   **clusterIP: None** (headless): DNS devuelve **todas** las IPs de los Pods; ideal para **StatefulSet** con identidad estable o clientes que necesitan sharding.

@section: kube-proxy y modos

Históricamente **iptables**; hoy muchos clústeres usan **IPVS** o delegan en el **CNI** (eBPF). El detalle importa al depurar latencias y conntrack.

@quiz: ¿Qué tipo de Service solo enruta tráfico interno al clúster por defecto?
@option: LoadBalancer
@correct: ClusterIP
@option: Ingress (recurso networking.k8s.io)
