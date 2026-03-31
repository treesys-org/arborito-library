@title: Multi-Container Patterns: Sidecar, Ambassador, Adapter
@icon: 🧩
@description: Composition in one pod, shared localhost, and responsibility boundaries.
@order: 3

# Multi-container patterns in a single pod

A **pod** can run multiple containers sharing **network** (localhost) and mounted **volumes**. Classic patterns **sidecar**, **ambassador**, and **adapter** structure responsibilities. This lesson explains when to use each and when a separate **Deployment** is better.

@section: Sidecar

A **sidecar** accompanies the main container to add cross-cutting features: **proxy** (Envoy), **log shipper**, **metrics exporter**. It shares the pod lifecycle: if the sidecar dies, the pod may restart depending on policy.

**Advantage:** atomic deploy of app + sidecar.  
**Risk:** coupling; scaling independently is harder than separate services.

@section: Ambassador

An **ambassador** proxy exposes a simplified local endpoint that translates to complex protocols or external services. The main app talks to `localhost` toward the ambassador.

**Use:** client sharding, retries, circuit breaking without changing app libraries.

@section: Adapter

An **adapter** normalizes output for external systems: e.g. transforms app logs to a standard format consumed downstream.

**Use:** integrating legacy systems that cannot change log/metric formats.

@section: When not to use multi-container

If you need to **scale** or **deploy** independently, separate **Services** are cleaner. If the sidecar is heavy and many pods do not need it, consider **DaemonSet** or cluster **service mesh**.

@section: Networking and DNS

Containers in the same pod share an **IP**; `localhost` works across ports. A **Service** targets the pod as a unit; you cannot route to a specific container without distinct ports.

@section: Common mistakes

* Two containers listening on the same port with **hostNetwork** without coordination.
* Sidecar without **resources** → CPU/memory contention with the main container.

@section: Suggested lab

1. Deploy a pod with nginx + a `busybox` sidecar writing shared logs to an `emptyDir` volume.
2. Expose distinct ports and verify with `kubectl exec` curl between containers.
3. Discuss with your team when to move the sidecar to a separate Deployment.

@quiz: Which pattern describes a local proxy that simplifies connecting to external services?
@option: Adapter
@correct: Ambassador
@option: Init container
