@title: Troubleshooting avanzado: señales, logs y red
@icon: 🔧
@description: Flujo sistemático desde Pending hasta NetworkPolicy y DNS.
@order: 5

# Troubleshooting avanzado: método cuando nada «obvio» falla

Los incidentes en Kubernetes suelen encadenar causas: **Scheduling** bloqueado, **image pull** roto, **probe** matando pods, **NetworkPolicy** silenciosa, **DNS** intermitente. Esta lección propone un **flujo** y herramientas: `kubectl describe`, `events`, `crictl`, **tcpdump**, **kube-proxy** / **CNI**.

@section: Mapa rápido de estados

* **Pending:** scheduling (recursos, taints, PVC), o **ImagePullBackOff** previo.
* **CrashLoopBackOff:** app, `exec`/`probe`, permisos.
* **Running pero not ready:** readiness, dependencias.

`kubectl describe pod` primero: **Events** al final son oro.

@section: Red

* **Service** mal etiquetado → endpoints vacíos (`kubectl get endpoints`).
* **NetworkPolicy** denegando por defecto en clusters restrictivos.
* **CoreDNS** caído → resolución falla cluster-wide.

**Pruebas:** `kubectl run curl --rm -it --image=curlimages/curl -- curl -v http://svc.ns.svc.cluster.local`.

@section: Storage

**PVC Pending:** StorageClass inexistente, quota, o provisioner roto. Revisa eventos del PVC y del PV.

**Permission denied** en volumen: `fsGroup`, SELinux.

@section: Nodos

**NotReady:** kubelet, PLEG, disco lleno, **cgroup** pressure. `journalctl -u kubelet` en el nodo (si tienes acceso).

@section: API y webhooks

Latencia alta en **mutating** webhooks o apiserver sobrecargado. **Audit logs** si están habilitados.

@section: Documentación del incidente

Anota hipótesis **antes** de cambiar; guarda outputs (`describe`, `logs`) en el ticket/postmortem.

@section: Errores frecuentes

* Reiniciar pods sin leer events.
* Asumir que el problema es la app cuando es **DNS** o **Service** mal configurado.

@section: Laboratorio sugerido

1. Rompe a propósito un `Service` selector en staging y sigue el flujo de diagnóstico hasta encontrarlo.
2. Aplica una `NetworkPolicy` demasiado estricta y observa timeouts.
3. Escribe una checklist personal de troubleshooting de 10 ítems.

@quiz: ¿Qué recurso deberías revisar si el Service existe pero no hay tráfico hacia los pods?
@option: Solo el Ingress
@correct: Endpoints del Service y que los labels coincidan con los pods
@option: Solo el ConfigMap
