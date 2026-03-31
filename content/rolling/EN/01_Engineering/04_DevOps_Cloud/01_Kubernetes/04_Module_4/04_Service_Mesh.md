
@title: Service mesh (operations view)
@icon: 🕸️
@description: Sidecar dataplane, mTLS, traffic management, operational cost.
@order: 4

# Service mesh

A **mesh** injects a **sidecar proxy** (often Envoy) next to each app container to intercept east-west traffic. **Istio**, **Linkerd**, and **Consul Connect** are common implementations.

@section: Problems it solves

*   **mTLS** between services without app changes.
*   Uniform **retries, timeouts, circuit breaking**.
*   **L7 observability** (HTTP routes, status codes) and distributed **tracing** headers.

@section: Costs

Extra **CPU/memory** per sidecar, **CRD/control-plane** complexity, and harder debugging if you do not understand the proxy.

@section: When to adopt

Useful when service count/teams grow and resilience logic is duplicated or skipped. For two services, solid **CNI + policies + observability** is often enough.

@section: Sidecar vs eBPF

Some approaches reduce or remove sidecars (ambient mesh, advanced CNI). Evaluate **kernel** constraints and multi-tenant needs.

@quiz: In a classic sidecar mesh, what typically intercepts the app container’s TCP traffic?
@option: kube-scheduler
@correct: A sidecar proxy (e.g. Envoy) in the same Pod
@option: Only iptables on a developer laptop
