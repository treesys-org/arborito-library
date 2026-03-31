@title: Security Contexts: User, Capabilities, and seccomp
@icon: 🛡️
@description: Pod- and container-level hardening, rootless, and privilege restrictions.
@order: 3

# Security contexts: harden pods without breaking apps

**securityContext** sets **user/group** (`runAsUser`), **capabilities**, **readOnlyRootFilesystem**, **seccomp**, and **AppArmor/SELinux** (node dependent). It is the first line of defense to **contain** container compromise.

@section: Pod vs container level

* **pod.spec.securityContext:** applies to all containers (fsGroup, supplementalGroups).
* **container.securityContext:** per-container overrides.

**runAsNonRoot:** fails startup if the image tries to run as root.

@section: Capabilities

Linux **capabilities** shrink root privileges. In Kubernetes you can **drop** `ALL` and **add** only what you need (`NET_BIND_SERVICE`, etc.).

The **image** must support ports >1024 or explicit capabilities.

@section: seccomp and profiles

**seccomp** filters syscalls. You can use **RuntimeDefault** or **Localhost** profiles (path to profile). Test in staging: strict profiles break poorly built binaries.

@section: readOnlyRootFilesystem

Mounts container **root** as read-only; requires **writes** to mounted volumes (`emptyDir`, tmpfs) for caches.

@section: allowPrivilegeEscalation

`allowPrivilegeEscalation: false` prevents processes from gaining more privileges (e.g. via setuid).

@section: Pod Security Standards

**PSS** (baseline, restricted) enforces policies per namespace; **Pod Security Admission** replaces PSP in many cases on recent versions.

@section: Common mistakes

* `runAsNonRoot` with images that **require** root (UID 0).
* Dropping capabilities without testing healthchecks that rely on `ping`/raw sockets.

@section: Suggested lab

1. Create a pod with `runAsUser: 1000` and verify with `kubectl exec id`.
2. Enable `readOnlyRootFilesystem` and add `emptyDir` for `/tmp` if needed.
3. Compare with **restricted** PSS in a test namespace.

@quiz: What does `readOnlyRootFilesystem: true` do on a container securityContext?
@option: Prevents mounting volumes
@correct: Prevents writes to the container root filesystem except on volumes mounted for that purpose
@option: Disables TLS
