
@title: Programación avanzada de Pods
@icon: 📍
@description: nodeSelector, afinidad, taints, tolerations y prioridades.
@order: 5

# Scheduling

El **scheduler** elige un nodo para cada Pod pendiente. Además de CPU/memoria solicitada, puedes guiar la colocación con **selectores**, **afinidad**, **taints** y **prioridades**.

@section: nodeSelector y nodeName

`nodeSelector` es el filtro más simple (labels en el nodo). `nodeName` fija el nodo y **salta** al scheduler—solo para casos especiales.

@section: Affinity / anti-affinity

*   **requiredDuringSchedulingIgnoredDuringExecution:** duro; si no hay nodo, el Pod queda **Pending**.
*   **preferred…:** blando; mejor esfuerzo.

Útil para **separar réplicas** en distintos hosts (`podAntiAffinity`) o colocar cerca de caches (`podAffinity`).

@section: Taints y tolerations

Los **taints** en el nodo **rechazan** Pods salvo que el Pod declare **toleration** coincidente. Patrón: nodos dedicados (`gpu=true:NoSchedule`), aislar cargas críticas, o **drain** (`NoExecute`).

@section: Prioridad y preemption

**PriorityClass** permite que Pods de alta prioridad **desalojen** (preempt) a otros en falta de recursos. Úsalo con cuidado: puede causar thrashing.

@section: PodDisruptionBudget

**PDB** limita cuántos Pods volátiles pueden estar fuera a la vez durante **drain** o eliminación voluntaria—protege disponibilidad sin bloquear mantenimiento si está bien calibrado.

@quiz: ¿Qué mecanismo permite que un nodo rechace Pods salvo que estos lo toleren explícitamente?
@option: nodeSelector únicamente
@correct: Taints en el nodo y tolerations en el Pod
@option: Solo el campo priorityClassName
