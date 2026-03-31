
@title: NetworkPolicies
@icon: 🛡️
@description: L3/L4 segmentation between Pods; default deny patterns.
@order: 4

# Network policies

By default **every Pod can reach every Pod** in the cluster. **NetworkPolicy** (requires a CNI that implements it: Calico, Cilium, Weave…) restricts **ingress**, **egress**, or both.

@section: Selectors

Policies target Pods with `podSelector` and optional `namespaceSelector`. You can also allow **ipBlock** CIDRs with `except` lists.

@section: Default deny pattern

1. A policy selecting all Pods in a namespace with **no** allowed ingress (and optionally deny egress).
2. Narrower policies that **allow** only required flows (front → api, api → db).

@section: DNS and egress

If you deny all egress, **CoreDNS** breaks—add an explicit allow to the `kube-dns` Service on UDP/TCP 53.

@section: Observability

Strict policies can cause **silent timeouts**. Use `kubectl exec` probes, CNI logs, or Hubble (Cilium) to see drops.

@quiz: What is a prerequisite for NetworkPolicy to work?
@option: Only NodePort Services
@correct: A CNI that implements NetworkPolicy
@option: Disabling kube-proxy always
