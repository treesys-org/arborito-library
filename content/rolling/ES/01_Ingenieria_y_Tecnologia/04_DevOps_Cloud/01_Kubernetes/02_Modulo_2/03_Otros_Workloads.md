
@title: StatefulSet, DaemonSet, Jobs y CronJobs
@icon: 🧩
@description: Cargas con identidad, una réplica por nodo, y trabajo por lotes.
@order: 3

# Otros controladores de carga

No todo encaja en un Deployment. Kubernetes ofrece primitivas para **estado**, **topología de nodo** y **ejecución batch**.

@section: StatefulSet

Pods con nombre **ordinal** estable (`app-0`, `app-1`), DNS headless y **PVC por réplica**. Útil para bases que replican por identidad (Kafka, etcd, algunos RDBMS en contenedor). **Orden** de creación/borrado es estricto; escalar y actualizar exige más cuidado que un Deployment.

@section: DaemonSet

Garantiza **un Pod por nodo** (o por subconjunto con selector de nodo). Típico: agentes de log, monitorización, CNI plugins, `kube-proxy` en modelos clásicos.

@section: Job

Ejecuta un Pod hasta **completar** exitosamente (`backoffLimit`, `ttlSecondsAfterFinished`). **CronJob** programa Jobs con sintaxis cron; vigila solapes (`concurrencyPolicy`) y historial (`successfulJobsHistoryLimit`).

@section: HorizontalPodAutoscaler

El **HPA** escala réplicas según CPU, memoria o métricas custom (Prometheus adapter). Requiere **metrics-server** u otro proveedor de métricas. No sustituye el dimensionado inicial sensato.

@quiz: ¿Qué workload da identidad estable y almacenamiento por réplica de forma habitual?
@option: Deployment con replicas: 1
@correct: StatefulSet
@option: Solo un Job
