
@title: Metrics, logs, and traces
@icon: 📈
@description: metrics-server, kubelet/cAdvisor, Prometheus stack, log pipelines.
@order: 3

# Operational observability

Running Kubernetes blind is guesswork. The usual pillars: **metrics**, **logs**, **traces**, plus **events**.

@section: Resource metrics

**metrics-server** scrapes CPU/memory usage via the kubelet **summary API** (not long-term storage). Powers `kubectl top` and basic **HPA**.

@section: Prometheus

Common pattern: **Prometheus Operator** or kube-prometheus-stack scraping **cAdvisor** (containers), **kube-state-metrics** (objects), and app exporters. **Alertmanager** routes to on-call tools.

@section: Logs

Container logs live on nodes. Ship them with **Fluent Bit**, **Vector**, or **Promtail** to Loki/Elasticsearch/OpenSearch. **`kubectl logs`** is ad hoc; it does not scale.

@section: Events

`kubectl get events --field-selector involvedObject.name=pod-x` correlates scheduling, image pull, and probe failures. **Event exporters** feed SIEMs in production.

@section: Golden signals

For each service track **latency**, traffic, errors, and saturation (RED/USE). Label with `namespace`, `deployment`, `pod` for team slices.

@quiz: What component typically backs `kubectl top pod`?
@option: etcd only
@correct: metrics-server (kubelet-sourced metrics)
@option: Ingress nginx without setup
