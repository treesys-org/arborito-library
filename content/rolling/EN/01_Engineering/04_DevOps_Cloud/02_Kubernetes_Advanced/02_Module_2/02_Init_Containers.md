@title: Init Containers: Preparation Before the Main Container
@icon: 🪜
@description: Sequential order, retries, and initialization patterns.
@order: 2

# Init containers: order and preconditions

**Init containers** run **before** normal pod containers, **in order**, until they succeed. They prepare volumes, wait for dependencies, run **migrations**, or fetch certificates. This lesson covers semantics, resources, and practical limits.

@section: Execution semantics

* They run **sequentially** in listed order.
* If one fails, Kubernetes retries according to the pod **restartPolicy**.
* All must exit **0** before normal containers start.

**Networking:** they share the pod **network namespace** (verify `localhost` behavior with your CNI version).

@section: Use cases

* **Wait** until an external dependency is ready (`kubectl wait` patterns, `nc -z`).
* **Clone** data into a shared `emptyDir`.
* Run **migrations** before the app starts (watch concurrency carefully).

**Anti-pattern:** long business logic in init: hurts observability and retries.

@section: Resources and limits

Init containers can have distinct **requests/limits**; the **scheduler** considers the **max** of init vs app for some resources (check your version docs).

**Images:** use small images pinned by digest in production.

@section: Security

* Do not mount broad credentials if read-only access suffices.
* **readOnlyRootFilesystem** when applicable.

@section: Comparison with hooks

**postStart** (main container hook) is different: runs alongside the app, does not guarantee strict ordering vs readiness. Init is better for preconditions.

@section: Common mistakes

* Init that never finishes (dependency down) → pod stuck `Init:0/1`.
* Assuming ordering between init and sidecars without checking the pod spec (sidecars are evolving in recent versions).

@section: Suggested lab

1. Create a pod with two init containers writing to a shared volume and a main container reading it.
2. Simulate failure in the second init and observe retries.
3. Measure startup time until `Ready`.

@quiz: When do normal pod containers start relative to init containers?
@option: In parallel from second one
@correct: Only after all init containers exit successfully
@option: Only if the init container uses the same image
