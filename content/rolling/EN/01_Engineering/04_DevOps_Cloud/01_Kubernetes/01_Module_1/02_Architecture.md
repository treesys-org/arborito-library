
@title: Kubernetes cluster architecture
@icon: 🏛️
@description: Control plane, worker nodes, CRI, CNI, and CSI in context.
@order: 2

# Kubernetes architecture

A cluster splits into the **control plane** and **worker nodes**. Desired state goes through the **API**; controllers converge actual state.

@section: Control plane

*   **kube-apiserver:** authenticated API entry; validates and persists desired state in **etcd**.
*   **etcd:** distributed consistent key-value store for all cluster objects.
*   **kube-scheduler:** binds unscheduled Pods to feasible nodes (resources, affinity, taints, priority…).
*   **kube-controller-manager:** runs **controllers** (ReplicaSet, Deployment, Node…) reconciling spec vs status.
*   **cloud-controller-manager** (cloud): integrates with the provider (LBs, routes, managed volumes).

@section: Worker node

*   **kubelet:** registers the node, receives Pod assignments, talks **CRI** to run containers, runs **probes**, reports status.
*   **kube-proxy** (or CNI dataplane): programs rules for **Services** (iptables/nftables/IPVS or eBPF).
*   **Container runtime:** containerd, CRI-O, etc.

@section: CNI, CSI, CRI

*   **CRI** — how the kubelet starts containers.
*   **CNI** — Pod networking across nodes.
*   **CSI** — external volume provisioning and attachment.

@section: Typical flow

1. You apply a manifest to the **apiserver**.
2. **etcd** stores the object; the **Deployment controller** reconciles **ReplicaSets** and **Pods**.
3. The **scheduler** picks a node; the node **kubelet** starts containers.
4. **CNI** assigns networking; **kube-proxy/CNI** routes traffic to **ClusterIP/NodePort**.

@quiz: Where is the desired state of API objects persisted?
@option: Only in kubelet files on each node
@correct: In etcd (via kube-apiserver)
@option: Only in the scheduler’s memory
