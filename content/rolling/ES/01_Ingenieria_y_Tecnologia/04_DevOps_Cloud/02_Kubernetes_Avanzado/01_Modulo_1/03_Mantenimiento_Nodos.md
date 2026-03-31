@title: Mantenimiento de nodos: cordon, drain y PDB
@icon: 🔧
@description: Evacuación segura de cargas, tiempo de terminación y coordinación con el scheduler.
@order: 3

# Mantenimiento de nodos: vaciar sin tumbar el servicio

Antes de apagar un nodo por parcheo o sustitución debes **evacuar** las cargas de trabajo de forma controlada. **Cordon**, **drain**, **PodDisruptionBudgets** y **grace periods** son las palancas. Esta lección explica el flujo y los errores típicos cuando el equipo presiona «reiniciar ya».

@section: Cordon

`kubectl cordon <node>` marca el nodo como **NoSchedule**: no recibe pods nuevos, pero los existentes siguen corriendo.

Úsalo como primer paso antes del drain para evitar que nuevas réplicas vuelvan a programarse ahí.

@section: Drain

`kubectl drain <node>` intenta **evictar** pods respetando ciertas reglas:

* Ignora pods **DaemonSet** (se recrean) salvo flags.
* Respeta **PDB** si está bien configurada; si no, puede bloquearse o forzar con riesgo.

Opciones importantes:

* `--ignore-daemonsets`
* `--delete-emptydir-data` (cuidado con datos locales efímeros)
* `--grace-period` alineado con el tiempo que la app necesita para cerrar conexiones.

@section: PodDisruptionBudget

Una **PDB** limita cuántas réplicas pueden estar **no disponibles** a la vez durante interrupciones voluntarias.

Ejemplo conceptual: `minAvailable: 2` en un Deployment de 3 réplicas → solo una puede desaparecer a la vez.

**Problemas:**

* PDB demasiado estricta con pocas réplicas → drain imposible.
* `minAvailable` en porcentaje con réplicas bajas → redondeos sorprendentes.

@section: Graceful shutdown

Las aplicaciones deben manejar **SIGTERM**: cerrar listeners, drenar requests, luego salir. `terminationGracePeriodSeconds` en el pod debe ser suficiente; si no, Kubernetes mata el proceso tras el plazo.

**Readiness** debe retirar el pod del Service **antes** de matar el proceso (patrón preStop en algunos casos).

@section: Nodos no gestionados vs gestionados

En **managed node groups**, a veces el proveedor reemplaza nodos automáticamente; aun así entender drain te ayuda en incidentes. En **bare metal**, tú controlas el ciclo completo.

@section: Errores frecuentes

* Drain sin revisar **local storage** (`emptyDir`) con datos críticos.
* Olvidar que un pod con **finalizers** puede colgar terminaciones.
* Forzar `--force` sin entender qué pods se pierden.

@section: Laboratorio sugerido

1. Crea un Deployment de 3 réplicas con PDB `minAvailable: 2`.
2. Ejecuta `cordon` y `drain` en un nodo de prueba y observa el orden de evicción.
3. Ajusta mal la PDB (imposible) y observa el bloqueo; corrige con réplicas o PDB.

@quiz: ¿Qué efecto tiene `kubectl cordon` en un nodo?
@option: Borra todos los pods al instante
@correct: Evita que se programen pods nuevos en ese nodo; los actuales siguen ejecutándose
@option: Actualiza el kubelet
