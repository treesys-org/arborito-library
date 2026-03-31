
@title: Monoliths, microservices, and containers
@icon: 📦
@description: Why orchestration appeared—from monoliths to the OCI container model.
@order: 1

# From monoliths to containers

For decades many applications shipped as **monoliths**: one process (or a few binaries) with business logic, persistence, and presentation coupled together. That simplifies early development, but **scaling** one bottleneck (API only, frontend only) forces you to scale the whole package, and releases are often all-or-nothing.

@section: Microservices

**Microservice** is not a product but an **architectural style**: many small services deployed independently, talking over the network (HTTP/gRPC/queues). Benefits: team autonomy, selective scaling, failure containment when designed well. Costs: **latency** between calls, **distributed consistency**, and the need for strong **observability** and **API contracts** (versioning).

Kubernetes does not require microservices: you can run monoliths in Pods. K8s shines when many units must be scheduled, restarted, and wired together.

@section: What a container is

A **container** bundles the app and dependencies (libraries, files) and shares the host **kernel** with other containers. It is not a full VM: it is a process tree isolated with **namespaces** (network, PID, mounts…) and **cgroups** (CPU/memory/I/O limits).

An **image** is an immutable artifact (read-only layers) built from a Dockerfile or equivalent; a **container** is a running instance of that image plus a writable layer.

@section: OCI and runtimes

**OCI** defines image format and runtime. In practice you will see **containerd** or **CRI-O** under Kubernetes; **Docker** remains a dev/build tool, while the kubelet talks **CRI** to the runtime.

@section: Why Kubernetes

At scale you need **scheduling**, **failure recovery**, **stable networking** to replica sets, **configuration and secrets**, **storage**, and **rolling updates**. That is the control plane + worker story in the next lesson.

@quiz: What do containers on the same Linux host typically share?
@option: A type-1 hypervisor and per-container guest kernel
@correct: The host kernel plus mechanisms such as namespaces and cgroups
@option: Always an identical root filesystem bit-for-bit for every container
