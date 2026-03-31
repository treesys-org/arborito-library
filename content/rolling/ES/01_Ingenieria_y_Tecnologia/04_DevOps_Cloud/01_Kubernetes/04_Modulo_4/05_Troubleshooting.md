
@title: Diagnóstico y resolución de incidencias
@icon: 🔧
@description: Pending, CrashLoop, ImagePull, red y herramientas efímeras.
@order: 5

# Troubleshooting sistemático

Empieza por **estado del Pod** y **eventos**, luego **logs**, luego **red/storage/RBAC**.

@section: Estados frecuentes

*   **Pending:** recursos, taints, **PVC** no bound, **image pull** secrets.
*   **CrashLoopBackOff:** app sale con error—mira **logs** y **previous** (`kubectl logs --previous`).
*   **ImagePullBackOff:** nombre de imagen, registry auth, rate limits.

@section: Comandos

```bash
kubectl describe pod POD -n NS
kubectl get events -n NS --sort-by=.lastTimestamp
kubectl logs POD -c CONT -n NS --tail=200
kubectl exec -it POD -n NS -- sh
```

@section: Debug ephemeral containers

En clústeres modernos, **debug** profile añade un contenedor efímero a un Pod en marcha (`kubectl debug`) para inspeccionar namespaces de red del Pod sin rebuild.

@section: apiserver y etcd

Si **todo** falla: salud del plano de control, certificados, disco de etcd, cuota de objetos. Revisa logs de `kube-apiserver` y métricas de latencia etcd.

@section: Runbook mínimo

1. ¿Es un problema de **una réplica** o de **todo el Deployment**?
2. ¿Cambió **Config/Secret** o **NetworkPolicy** recientemente?
3. ¿Hay **PDB** bloqueando eviction o **Quota** agotada?

@quiz: ¿Qué comando suele mostrar razones de scheduling y eventos recientes de un Pod?
@option: kubectl get pods -o wide únicamente
@correct: kubectl describe pod …
@option: kubectl cluster-info dump siempre
