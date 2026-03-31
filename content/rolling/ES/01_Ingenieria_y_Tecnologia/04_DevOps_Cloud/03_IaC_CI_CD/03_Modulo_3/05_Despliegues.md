@title: Estrategias de despliegue: rolling, blue-green y canary
@icon: 🚀
@description: Reducción de riesgo, rollback y encaje con Kubernetes e Istio.
@order: 5

# Estrategias de despliegue: velocidad con control de riesgo

Desplegar una nueva versión no es solo «reemplazar binarios»: elige **cómo** introduces tráfico y **cómo** vuelves atrás si algo falla. Esta lección resume patrones clásicos y su encaje con balanceadores, Kubernetes y service mesh.

@section: Rolling update

**Rolling update** reemplaza instancias gradualmente **N a la vez**. Ventaja: sin duplicar toda la capacidad. Desventaja: durante la ventana coexisten **dos versiones**; los clientes pueden ver comportamientos mixtos si no hay compatibilidad hacia atrás en APIs.

En Kubernetes: `Deployment` con `maxSurge` y `maxUnavailable` controlan el ritmo.

@section: Blue-green

**Blue-green** mantiene **dos entornos completos** (blue actual, green nuevo). Tras validar green, **conmutas el tráfico** de golpe (DNS, balanceador, tabla de rutas). Si falla, vuelves a blue.

Coste: **doble capacidad** temporalmente. Ideal cuando necesitas validación fuerte antes de cortar.

@section: Canary

**Canary** dirige un **porcentaje pequeño** de usuarios o peticiones a la nueva versión y observa métricas (errores, latencia). Si todo va bien, aumentas el porcentaje hasta el 100%.

Implementación: balanceador con pesos, **Istio/Linkerd** (traffic split), **feature flags** combinados con métricas.

@section: Recreate

**Recreate** baja todo lo viejo antes de subir lo nuevo. Hay **downtime**; solo aceptable en entornos de mantenimiento o jobs batch.

@section: Rollback

**Rollback** no es magia: necesitas:

* Versiones de artefacto **direccionables** (tags de imagen, releases).
* Migraciones de base de datos **compatibles** (expand/contract pattern) o scripts reversibles.
* Runbook que diga qué métricas mirar antes de declarar éxito.

En Kubernetes: `kubectl rollout undo` para `Deployment` si la revisión anterior sigue disponible.

@section: Bases de datos y despliegues

Los cambios de esquema rompen despliegues si la app vieja y nueva no comparten el mismo contrato. Patrones:

* **Expand:** añadir columnas nullable primero.
* **Contract:** eliminar después de que todas las versiones usen el nuevo campo.

@section: Observabilidad durante el despliegue

Define **SLIs** (latencia p95, tasa de error) y **SLOs**; alerta si el canary empeora respecto a la línea base. **Logs** estructurados con `version` o `deployment_id` ayudan a correlacionar.

@section: Errores frecuentes

* Desplegar viernes tarde sin dueño de rollback.
* Asumir que blue-green elimina la necesidad de pruebas de compatibilidad de API.
* Ignorar migraciones de datos en el plan de despliegue.

@section: Laboratorio sugerido

1. Con un `Deployment` de prueba en Kubernetes, ajusta `maxUnavailable` y observa el tiempo de actualización.
2. Simula un fallo en la nueva versión y practica `rollout undo`.
3. Escribe un runbook de una página: señales de fallo, quién ejecuta rollback, cómo verificar recuperación.

@quiz: ¿Qué desventaja principal tiene un rolling update frente a blue-green?
@option: Siempre duplica la infraestructura
@correct: Durante la ventana coexisten dos versiones; puede haber inconsistencias si la API no es compatible
@option: No permite rollback
