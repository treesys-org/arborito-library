
@title: Métricas, logs y trazas
@icon: 📈
@description: metrics-server, kubelet cadvisor, Prometheus ecosystem y agregación de logs.
@order: 3

# Observabilidad operativa

Operar Kubernetes sin señales es adivinar. Las tres patas clásicas: **métricas**, **logs**, **trazas** (y a veces **eventos** del plano de control).

@section: Métricas de recursos

**metrics-server** recopila uso CPU/mem desde el **summary API** del kubelet (no es almacén histórico largo). Sirve para `kubectl top` y **HPA** básico.

@section: Prometheus

Patrón común: **Prometheus Operator** o kube-prometheus-stack; scrape de `cadvisor` (contenedor), `kube-state-metrics` (objetos K8s), exporters de apps. **Alertmanager** enruta a PagerDuty/Slack.

@section: Logs

Los logs de contenedor viven en el nodo (container runtime). Agéntelos con **Fluent Bit**, **Vector** o **Promtail** hacia Loki/Elasticsearch/OpenSearch. **kubectl logs** es puntual; no escala.

@section: Eventos

`kubectl get events --field-selector involvedObject.name=pod-x` correlaciona fallos de scheduling, pulls, probes. En producción, **event exporters** envían a SIEM.

@section: Golden signals

Para cada servicio: **latencia**, tráfico, errores, saturación (USE/RED). Etiqueta con `namespace`, `deployment`, `pod` para cortar por equipo.

@quiz: ¿Qué componente suele alimentar el comando kubectl top pod?
@option: Solo etcd
@correct: metrics-server (datos agregados del kubelet)
@option: Ingress nginx sin configuración
