
@title: Deployments y actualizaciones continuas
@icon: 🚀
@description: ReplicaSet, estrategia RollingUpdate, rollback y sondas de vida.
@order: 1

# Deployments

Un **Deployment** declara aplicaciones **sin estado** (stateless) que deben correr en N réplicas. Crea y posee un **ReplicaSet** que a su vez crea **Pods** con la plantilla dada. Al cambiar la imagen o la plantilla, el Deployment orquesta una **sustitución controlada** de Pods.

@section: ReplicaSet

El ReplicaSet garantiza que existan **siempre** un número deseado de Pods que coincidan con su **selector**. Si un nodo cae, se programan Pods nuevos en otro sitio. El Deployment añade **historial de revisiones** y **estrategia de despliegue** encima del ReplicaSet.

@section: RollingUpdate

Por defecto `strategy.type: RollingUpdate` con `maxUnavailable` y `maxSurge` (pueden ser porcentajes). Kubernetes crea Pods nuevos antes (o después) de terminar viejos según esos límites, manteniendo capacidad acotada.

```yaml
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
```

@section: Rollback

Cada cambio genera una **ReplicaSet** antigua conservada (según `revisionHistoryLimit`):

```bash
kubectl rollout history deployment/mi-app
kubectl rollout undo deployment/mi-app
kubectl rollout status deployment/mi-app
```

@section: Probes

*   **livenessProbe:** reinicia el contenedor si falla.
*   **readinessProbe:** saca el Pod del balanceo del Service hasta que pase.
*   **startupProbe** (opcional): da margen a arranques lentos antes de aplicar liveness.

Define `httpGet`, `tcpSocket` o `exec` con `initialDelaySeconds` y `periodSeconds` realistas para no matar servicios sanos.

@quiz: ¿Qué objeto posee directamente los Pods creados a partir de una plantilla de Deployment?
@option: El Deployment, sin intermediarios
@correct: El ReplicaSet gestionado por el Deployment
@option: Solo el Service asociado
