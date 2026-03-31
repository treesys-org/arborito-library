
@title: Services and discovery
@icon: 🔌
@description: ClusterIP, NodePort, LoadBalancer, Endpoints, and headless Services.
@order: 2

# Services

Pods are **ephemeral** and change IPs. A **Service** provides a **stable virtual IP** (**ClusterIP**) and **DNS** (`<svc>.<ns>.svc.cluster.local`) that load-balances to Pods via **selectors** or manual **Endpoints**.

@section: ClusterIP

Default type: traffic **inside** the cluster only. kube-proxy (or the CNI dataplane) programs DNAT to Pod IPs.

@section: NodePort

Opens a port on **every node** toward the Service. Handy in labs; production often fronts it with **LoadBalancer** or Ingress.

@section: LoadBalancer

Asks the **cloud controller** for an external VIP. On bare metal you need MetalLB, kube-vip, or similar.

@section: Headless and non-selector Services

*   No selector: you maintain **Endpoints** / **EndpointSlices** manually (legacy or external backends).
*   **clusterIP: None** (headless): DNS returns **all** Pod A/AAA records—useful for **StatefulSet** identity or client-side sharding.

@section: kube-proxy modes

Historically **iptables**; many clusters use **IPVS** or delegate to **eBPF** CNIs. This matters when debugging latency and conntrack.

@quiz: Which Service type routes internal cluster traffic by default?
@option: LoadBalancer
@correct: ClusterIP
@option: An Ingress resource (networking.k8s.io)
