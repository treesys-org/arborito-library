
@title: Troubleshooting playbooks
@icon: 🔧
@description: Pending, CrashLoop, ImagePull, networking, ephemeral debug.
@order: 5

# Systematic troubleshooting

Start with **Pod phase** and **events**, then **logs**, then **network/storage/RBAC**.

@section: Common states

*   **Pending:** resources, taints, **unbound PVC**, image pull secrets.
*   **CrashLoopBackOff:** exiting process—check logs and **`kubectl logs --previous`**.
*   **ImagePullBackOff:** wrong image name, registry auth, rate limits.

@section: Commands

```bash
kubectl describe pod POD -n NS
kubectl get events -n NS --sort-by=.lastTimestamp
kubectl logs POD -c CONT -n NS --tail=200
kubectl exec -it POD -n NS -- sh
```

@section: Ephemeral debug containers

`kubectl debug` can attach a short-lived container to a running Pod to inspect its network namespace without rebuilding the image.

@section: Control plane

If **everything** fails: apiserver health, etcd disk/latency, certificates. Check `kube-apiserver` logs and etcd metrics.

@section: Minimal runbook

1. Is it **one replica** or the whole **Deployment**?
2. Did **Config/Secret/NetworkPolicy** change recently?
3. Is a **PDB** or **Quota** blocking eviction or scheduling?

@quiz: Which command usually shows scheduling reasons and recent Pod events?
@option: kubectl get pods -o wide only
@correct: kubectl describe pod …
@option: kubectl cluster-info dump always
