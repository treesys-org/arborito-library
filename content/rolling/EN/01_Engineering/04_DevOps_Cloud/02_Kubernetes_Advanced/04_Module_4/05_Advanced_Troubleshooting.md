@title: Advanced Troubleshooting: Signals, Logs, and Networking
@icon: 🔧
@description: A systematic flow from Pending to NetworkPolicy and DNS.
@order: 5

# Advanced troubleshooting: a method when nothing “obvious” fails

Kubernetes incidents often chain causes: **scheduling** blocked, broken **image pull**, **probes** killing pods, silent **NetworkPolicy**, flaky **DNS**. This lesson proposes a **flow** and tools: `kubectl describe`, `events`, **crictl**, **tcpdump**, **kube-proxy** / **CNI**.

@section: Quick state map

* **Pending:** scheduling (resources, taints, PVC) or prior **ImagePullBackOff**.
* **CrashLoopBackOff:** app, `exec`/probe, permissions.
* **Running but not ready:** readiness, dependencies.

`kubectl describe pod` first: **Events** at the bottom are gold.

@section: Networking

* Mislabeled **Service** → empty endpoints (`kubectl get endpoints`).
* **NetworkPolicy** denying by default in restrictive clusters.
* **CoreDNS** down → cluster-wide DNS failures.

**Tests:** `kubectl run curl --rm -it --image=curlimages/curl -- curl -v http://svc.ns.svc.cluster.local`.

@section: Storage

**PVC Pending:** missing StorageClass, quota, or broken provisioner. Check PVC/PV events.

**Permission denied** on volumes: `fsGroup`, SELinux.

@section: Nodes

**NotReady:** kubelet, PLEG, full disk, **cgroup** pressure. `journalctl -u kubelet` on the node (if you have access).

@section: API and webhooks

High latency on **mutating** webhooks or overloaded apiserver. **Audit logs** if enabled.

@section: Incident documentation

Note hypotheses **before** changing things; save outputs (`describe`, `logs`) in tickets/postmortems.

@section: Common mistakes

* Restarting pods without reading events.
* Assuming the app is wrong when **DNS** or **Service** selectors are misconfigured.

@section: Suggested lab

1. Intentionally break a `Service` selector in staging and follow diagnostics until you find it.
2. Apply an overly strict `NetworkPolicy` and observe timeouts.
3. Write a personal 10-item troubleshooting checklist.

@quiz: What should you check if the Service exists but no traffic reaches pods?
@option: Only Ingress
@correct: Service Endpoints and matching pod labels
@option: Only ConfigMap
