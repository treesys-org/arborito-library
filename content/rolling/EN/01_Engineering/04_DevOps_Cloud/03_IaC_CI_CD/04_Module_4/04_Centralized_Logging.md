@title: Centralized Logging: Loki, ELK, and Correlation
@icon: 📜
@description: Aggregation, indexes, retention, and tying logs to metrics and traces.
@order: 4

# Centralized logging: find needles without taking down the cluster

**Logs** are lines of text (or JSON) emitted by applications and operating systems. On dozens of machines or thousands of pods, **grep over SSH** does not scale: you need **aggregation**, **search**, and **retention** with clear policies. This lesson contrasts approaches (ELK, Loki) and connects logs to metrics and traces.

@section: What a log pipeline is

1. **Collection:** node agent (Fluent Bit, Fluentd, Promtail) or sidecar.
2. **Transport:** Kafka, HTTP, syslog.
3. **Ingestion and indexing:** Elasticsearch, OpenSearch, Loki, cloud vendors.
4. **Query:** Kibana, Grafana Explore, proprietary UIs.

Cost usually sits in **storage** and **indexes**; field **cardinality** matters as much as for metrics.

@section: ELK / OpenSearch

**Elasticsearch** indexes full text with powerful analyzers. **Kibana** visualizes and builds dashboards. **OpenSearch** is a compatible open-source fork.

Strengths: complex search, aggregations. Weaknesses: **operating large clusters** is a full-time job; disk and poorly managed indexes explode costs.

@section: Grafana Loki

**Loki** labels logs like Prometheus (labels) and stores compressed bodies in **object storage** (S3, GCS). **LogQL** combines label filters and content search.

Advantage: simpler cost and operations aligned with the Prometheus ecosystem. Limitation: not as rich a full-text engine as Elasticsearch for every use case.

@section: Structured format

**Structured JSON** (`{"level":"error","trace_id":"..."}`) eases filtering and correlation. Include **trace_id** and **span_id** if you use distributed tracing.

Consistent **levels** (`INFO`, `WARN`, `ERROR`) reduce noise in alerts.

@section: Retention and sampling

Define **retention** by log type (application vs audit). Some teams **sample** high-volume access logs and keep 100% only during incidents.

**PII:** mask or tokenize personal data before sending to shared systems.

@section: Correlation with metrics and traces

From Grafana: jump from a Prometheus spike to logs filtered by `pod` or `trace_id`. **Exemplars** in Prometheus link series to trace IDs in some setups.

@section: Common mistakes

* Logging secrets or tokens (leakage into the aggregator).
* High-cardinality fields in Elasticsearch indexes (shard explosion).
* No rotation policy → full disk and cluster failure.

@section: Suggested lab

1. Deploy Promtail + Loki + Grafana locally (official Grafana docker-compose).
2. Send JSON logs from a test app and explore them in **Explore** with LogQL.
3. Write an alert rule on `level=error` log rate if your stack supports it.

@quiz: What is the main difference between Loki and Elasticsearch for logs?
@option: Loki cannot label logs
@correct: Loki indexes strongly by labels (Prometheus-style) and pushes text to object storage; Elasticsearch full-text indexing is heavier
@option: Elasticsearch only works on Windows
