@title: Schedulers personalizados y múltiples perfiles
@icon: 🗓️
@description: scheduling profiles, extensión y cuándo usar scheduler alternativo.
@order: 5

# Múltiples schedulers y perfiles: más allá del default

El **kube-scheduler** estándar asigna pods a nodos según **filtros** y **puntuación** (plugins). A veces necesitas **perfiles** distintos (batch vs latency) o un **scheduler** alternativo. Esta lección introduce **Scheduling Profiles**, **schedulerName** y límites de operación.

@section: Scheduler por defecto

El scheduler considera:

* **Resource requests**, **taints/tolerations**, **node affinity**, **pod affinity/anti-affinity**, **topology spread**, **prioridad**.
* Plugins habilitados vía **KubeSchedulerConfiguration**.

**Extensión:** muchos casos se resuelven con **configuración** en lugar de binario custom.

@section: scheduling profiles

Desde versiones recientes puedes definir **múltiples perfiles** en un solo binario scheduler con `schedulerName` distinto en el pod (según configuración del cluster).

**Caso de uso:** workloads batch con prioridad baja y plugins diferente.

@section: Scheduler alternativo

Puedes desplegar otro **kube-scheduler** con su propia configuración y referenciarlo:

```yaml
spec:
  schedulerName: my-custom-scheduler
```

Requiere **RBAC**, **leader election** y **compatibilidad** con la versión del cluster.

**No** es trivial: la mayoría de equipos evita schedulers custom salvo requisitos fuertes.

@section: Cuándo no hace falta

Antes de un scheduler nuevo, evalúa:

* **PriorityClass** y **preemption**.
* **Topology spread constraints**.
* **Taints** y pools de nodos.

@section: Depuración

* Eventos del pod (`FailedScheduling`) con razones.
* Logs del scheduler con `--v` para ver scoring.

@section: Errores frecuentes

* `schedulerName` apuntando a scheduler inexistente → pods en `Pending` eterno.
* Dos schedulers con conflictos de **leader election** mal configurados.

@section: Laboratorio sugerido

1. Inspecciona la configuración del scheduler por defecto en `kube-system` (según acceso).
2. Crea un pod con `priorityClassName` y observa preemption en entorno de prueba.
3. Lee la documentación de **scheduling profiles** para tu versión y resume cuándo usarlas.

@quiz: ¿Qué campo del pod selecciona qué scheduler lo coloca?
@option: priorityClassName
@correct: schedulerName
@option: nodeName
