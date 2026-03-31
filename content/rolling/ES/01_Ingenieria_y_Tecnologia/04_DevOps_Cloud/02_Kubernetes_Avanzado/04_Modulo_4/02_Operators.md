@title: Operators: control loops y el patrón de Kubernetes
@icon: 🤖
@description: Reconcile loop, SDKs y operación de sistemas con estado
@order: 2

# Operators: automatizar operaciones con código

Un **Operator** es un controlador que observa **CRDs** y ejecuta **reconcile**: lleva el estado real (clusters externos, bases de datos) hacia el **spec** deseado. **etcd**, **Kafka**, **Postgres** en Kubernetes suelen gestionarse con Operators maduros. Esta lección describe el **patrón**, **SDKs** y riesgos.

@section: Bucle reconcile

1. Observar eventos del recurso y dependencias.
2. Comparar estado deseado vs real.
3. Actuar (crear StatefulSets, Secrets, backups).
4. Actualizar **status** y reencolar si falla (backoff).

**Idempotencia:** reintentos deben ser seguros.

@section: Frameworks

* **Operator SDK** (Go, Ansible, Helm).
* **Kubebuilder** genera proyectos Go.
* **Kopf** (Python) para prototipos.

**Helm Operator** envuelve charts; útil si ya tienes Helm.

@section: Upgrade y migraciones

Operators deben manejar **upgrades** de versión del operando sin pérdida de datos. Lee **release notes** del Operator (CRD changes, campos renombrados).

@section: Riesgos

* Operators con **cluster-wide** RBAC amplios.
* Bugs que borran PVCs o Secretos.

**Vendor lock-in** parcial: tus datos siguen siendo tuyos, pero el CRD es contrato.

@section: Observabilidad

Expón **métricas** del Operator (reconcile duration, errors). **Logs** estructurados con `namespace/name` del recurso.

@section: Laboratorio sugerido

1. Instala un Operator de demo en cluster de prueba (p.ej. prometheus-operator mínimo).
2. Observa pods del operator y CRs creadas.
3. Simula error en spec y revisa eventos/status.

@quiz: ¿Qué hace principalmente la función de reconcile de un Operator?
@option: Solo crear pods aleatorios
@correct: Alinear el estado real del sistema con el spec declarado en el recurso custom
@option: Formatear YAML
