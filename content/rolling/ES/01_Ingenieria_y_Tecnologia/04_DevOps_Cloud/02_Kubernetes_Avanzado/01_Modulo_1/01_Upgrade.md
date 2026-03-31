@title: Actualización de versión del clúster (upgrade)
@icon: ⬆️
@description: Planificación de upgrades de control plane y nodos, compatibilidad de API y ventanas de cambio.
@order: 1

# Actualización de clúster Kubernetes: sin sorpresas en producción

Los **upgrades** de Kubernetes no son un `apt upgrade` invisible: implican **nueva versión del control plane**, posiblemente **nueva versión de nodos**, cambios de **APIs** deprecadas y comportamiento distinto del **scheduler** o del **kubelet**. Esta lección describe el procedimiento mental: leer release notes, actualizar en saltos soportados, validar workloads y tener plan de **rollback**.

@section: Version skew y compatibilidad

Kubernetes mantice reglas de **skew** entre componentes (kube-apiserver, kubelet, kubectl). Antes de planificar:

* Lee las notas de la versión destino en **kubernetes.io** (cambios de comportamiento, APIs removidas).
* Verifica la matriz de tu distribución (EKS, GKE, AKS, kubeadm) sobre **versiones intermedias** obligatorias.

**Regla práctica:** no te saltes varias minors sin confirmar el camino soportado por tu proveedor.

@section: Control plane vs nodos

En la mayoría de entornos gestionados el **control plane** lo actualiza el proveedor o un proceso interno; tú gestionas **node groups** o **MachineDeployments**. En instalaciones **kubeadm**, el orden típico documentado es: actualizar **control plane** primero, luego **nodos**, manteniendo skew dentro de lo permitido.

Los **DaemonSets** (CNI, monitorización) deben ser compatibles con la nueva versión antes del corte.

@section: Deprecaciones de API

`kubectl convert` ya no es la solución mágica: debes migrar manifiestos de APIs **beta** retiradas (`extensions/v1beta1` Ingress antiguos, etc.) **antes** del upgrade. Usa:

* `kubectl get ingress -A -o yaml` y revisa `apiVersion`.
* Herramientas como **pluto** o políticas en CI que fallen si aparecen APIs obsoletas.

@section: Estrategia de rollout de nodos

* **Sustituir** nodos (nuevo ASG/NG) suele ser más limpio que `apt upgrade` in-place en todos los casos.
* **Cordon + drain** nodos viejos tras desalojar cargas con **PodDisruptionBudgets** respetadas.

**PDB** mal configurada puede bloquear el drain indefinidamente: revisa `minAvailable` vs réplicas reales.

@section: Pruebas previas

* **Entorno de staging** con la misma versión objetivo y mismos charts/Operators.
* **Pruebas de carga** y smoke tests automatizados post-upgrade.
* Verifica **CSI drivers**, **Ingress controllers** y **service mesh** contra la matriz de compatibilidad.

@section: Rollback

En cloud gestionado a veces el rollback del control plane **no** está disponible o es limitado. Planifica:

* Versiones de imagen de aplicación fijadas por tag.
* **Velero** o backups de etcd (ver lección de backup) si gestionas el plano de control tú mismo.

@section: Ventana de cambio y comunicación

Documenta dueño, criterios de éxito (latencia, tasa de error) y **abort criteria** (volver atrás si SLO se rompe). Comunica a equipos de plataforma y producto.

@section: Errores frecuentes

* Actualizar sin leer deprecaciones → pods en `CrashLoopBackOff` por CRDs rotas.
* Olvidar actualizar **CNI** o **metrics-server** acoplados a la versión.
* Mezclar versiones de `kubectl` muy nuevas contra clusters viejos sin probar scripts.

@section: Laboratorio sugerido

1. En un cluster de prueba (kind, minikube o sandbox cloud), sube **un** minor siguiendo la guía oficial de tu herramienta.
2. Ejecuta `kubectl api-resources` antes y después y anota diferencias relevantes.
3. Simula un `kubectl drain` de un nodo con una app de prueba y PDB.

@quiz: ¿Qué debes revisar antes de un upgrade respecto a tus manifiestos?
@option: Solo el tamaño de los pods
@correct: APIs deprecadas o retiradas que puedan impedir que recursos se re-creen correctamente
@option: Solo el color del tema de kubectl
