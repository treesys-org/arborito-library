@title: Control Plane High Availability
@icon: 🏗️
@description: etcd topologies, kube-apiserver load balancing, and cost trade-offs.
@order: 5

# Control plane high availability

A **production-grade** cluster needs the **control plane** to survive node failures and partial network cuts. This lesson summarizes **topologies** (stacked vs external), **apiserver load balancing**, **etcd quorum**, and cost trade-offs without replacing each distribution’s manual.

@section: Control plane components

* **kube-apiserver:** HTTPS API front end.
* **etcd:** state store.
* **kube-scheduler:** assigns pods to nodes.
* **kube-controller-manager:** controllers (Deployment, ReplicaSet, etc.).
* **cloud-controller-manager:** cloud integration (LBs, routes) depending on environment.

In managed clouds many details are **hidden** but still exist under the hood.

@section: etcd and quorum

etcd requires a **majority quorum**: typically **3** nodes tolerate one failure; **5** tolerate two. Use an **odd** member count.

**Do not** scale etcd membership for performance without guidance: more nodes ≠ linear read/write throughput.

@section: kube-apiserver HA

Multiple **apiserver** instances behind a **TLS load balancer** (internal). Clients (kubelets, users) target the VIP or DNS name.

Certificates must include **SANs** for the LB name and possibly IPs.

@section: Stacked vs external etcd

* **Stacked:** etcd runs on the same hosts as the control plane (typical small kubeadm).
* **External etcd:** dedicated cluster, useful for large deployments or operational separation.

Cloud providers often manage etcd **opaquely** with an SLA.

@section: Impact on workloads

Control plane HA does **not** replace application HA: you need replicas, PDBs, replicated storage, and stateless design where possible.

@section: Zones and regions

* **Multi-AZ** within a region: tolerates AZ failures with low latency.
* **Multi-region** active/active is **much** harder (databases, consistency); often active/passive or sharding.

@section: Common mistakes

* Single apiserver without LB (logical or network SPOF).
* etcd backups without **tested** restore.
* Under-provisioned etcd IOPS (whole control plane becomes slow).

@section: Suggested lab

1. Sketch a three-node control plane with LB in front of apiservers and a three-member etcd cluster.
2. List what fails first if you lose one node vs two etcd members in a three-member cluster.
3. Read your cloud provider’s control plane SLA and compare it to your application SLOs.

@quiz: Why do etcd clusters usually have an odd number of members?
@option: Kubernetes requires it for all pods
@correct: For majority voting (quorum) in Raft consensus
@option: Because odd numbers are cheaper
