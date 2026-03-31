@title: Requests, limits y QoS: CPU y memoria
@icon: ⚖️
@description: Cgroups, eviction, Guaranteed vs Burstable y presión sobre el nodo.
@order: 4

# Recursos de CPU y memoria: lo que el scheduler y el kubelet imponen

**Requests** guían al **scheduler** (qué cabe en nodo). **Limits** acotan al **kubelet** vía cgroups en el runtime. Sin límites, un pod puede **matar** el nodo por OOM. Esta lección explica **QoS classes**, **compresión de CPU**, **evicción** y buenas prácticas.

@section: Requests y limits

* **requests:** reserva mínima esperada; el scheduler usa para `allocatable`.
* **limits:** máximo permitido (memoria dura; CPU puede ser throttle según configuración).

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

@section: Clases QoS

* **Guaranteed:** requests == limits para todos los contenedores (más protegido ante presión).
* **Burstable:** requests < limits o no definidos uniformemente.
* **BestEffort:** sin requests ni limits (primero en evicción).

**OOMKilled:** el contenedor supera límite de memoria → reinicio.

@section: CPU: shares y CFS

CPU suele **compartirse** por pesos; **throttling** si el contenedor consume más que su cuota. Síntomas: latencia alta sin OOM. Usa **metrics** (cAdvisor) para ver throttling.

@section: Evicción de nodo

Cuando el nodo queda sin memoria o disco, el kubelet **evicta** pods en orden según QoS y prioridad. **BestEffort** primero.

**ephemeral-storage** requests/limits importan para logs y capas de escritura.

@section: Horizontal Pod Autoscaler

**HPA** escala réplicas según CPU/mem/custom metrics; requiere **metrics-server** u otro adapter. **VPA** ajusta requests (cuidado con interacción).

@section: Errores frecuentes

* `limits` sin `requests` → scheduling impredecible.
* Ignorar **ephemeral storage** en apps que escriben mucho a disco local.
* No configurar **PodDisruptionBudget** al escalar con HPA agresivo.

@section: Laboratorio sugerido

1. Crea pods con QoS distinto y genera presión de memoria en un nodo de prueba (cuidado).
2. Observa eventos de evicción y orden de terminación.
3. Configura HPA con `metrics-server` para un Deployment de prueba.

@quiz: ¿Qué clase QoS suele ser la primera candidata a evicción bajo presión de memoria en el nodo?
@option: Guaranteed
@correct: BestEffort
@option: Burstable siempre antes que BestEffort
