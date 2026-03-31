@title: Patrones multi-contenedor: sidecar, ambassador y adapter
@icon: 🧩
@description: Composición en un pod, localhost compartido y límites de responsabilidad.
@order: 3

# Patrones multi-contenedor en un mismo pod

Un **pod** puede tener varios contenedores que comparten **red** (localhost) y **volúmenes** montados. Los patrones clásicos **sidecar**, **ambassador** y **adapter** estructuran responsabilidades. Esta lección explica cuándo usar cada uno y cuándo es mejor **otro Deployment**.

@section: Sidecar

Un **sidecar** acompaña al contenedor principal para añadir funcionalidad transversal: **proxy** (Envoy), **log shipper**, **metric exporter**. Comparte ciclo de vida del pod: si el sidecar muere, el pod puede reiniciarse según política.

**Ventaja:** despliegue atómico de app + sidecar.  
**Riesgo:** acoplamiento: escalar por separado es más difícil que servicios separados.

@section: Ambassador

Un **ambassador** proxy expone un endpoint local simplificado que traduce a protocolos complejos o servicios externos. La app principal habla a `localhost` hacia el ambassador.

**Uso:** sharding de clientes, retries, circuit breaking en el lado cliente sin tocar librerías de la app.

@section: Adapter

Un **adapter** normaliza salida para el exterior: por ejemplo transforma logs de la app a un formato estándar que **otro** sistema consume.

**Uso:** integración con sistemas legacy que no pueden cambiar formato de logs/métricas.

@section: ¿Cuándo no usar multi-contenedor?

Si necesitas **escalar** o **desplegar** por separado, **Services** distintos son más limpios. Si el sidecar es pesado y muchos pods no lo necesitan, considera **DaemonSet** o **service mesh** a nivel de cluster.

@section: Red y DNS

Contenedores en el mismo pod comparten **IP**; resolución `localhost` funciona entre puertos. **Service** apunta al pod como unidad; no enrutas tráfico a un contenedor específico sin puertos distintos.

@section: Errores frecuentes

* Dos contenedores escuchando el mismo puerto en **hostNetwork** sin coordinación.
* Sidecar sin **resources** → competencia por CPU/mem con el principal.

@section: Laboratorio sugerido

1. Despliega un pod con nginx + sidecar de `busybox` que escriba logs compartidos en un volumen `emptyDir`.
2. Expón puertos distintos y verifica con `kubectl exec` curl entre contenedores.
3. Discute con el equipo cuándo mover el sidecar a un Deployment separado.

@quiz: ¿Qué patrón describe un proxy local que simplifica la conexión a servicios externos?
@option: Adapter
@correct: Ambassador
@option: Init container
