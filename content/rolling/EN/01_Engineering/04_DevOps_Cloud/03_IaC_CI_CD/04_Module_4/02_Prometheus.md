@title: Prometheus: Data Model, Scraping, and PromQL
@icon: 📈
@description: Time-series metrics, jobs, labels, alerts, and cardinality best practices.
@order: 2

# Prometheus: metrics for cloud-native systems

**Prometheus** is a monitoring and alerting system for **time series**. It collects metrics via HTTP **pull** (`/metrics`) from discovered targets (Kubernetes, exporters, your app). This lesson covers the data model, **PromQL**, alerts, and **cardinality** traps.

@section: Short architecture

* **Prometheus server:** scrape, local storage (TSDB), rule evaluation.
* **Service discovery:** Kubernetes, Consul, DNS, static files.
* **Alertmanager:** routes, deduplicates, groups, and notifies (PagerDuty, Slack, email).
* **Pushgateway:** exception for batch jobs that cannot be scraped (use carefully).

**Federation** and **Thanos/Cortex/Mimir** scale reads and retention in large environments.

@section: Data model

Each sample is a **series** identified by:

* **Metric name** (`http_requests_total`).
* **Labels** (`method="GET"`, `status="200"`).

Common client library types: **counter** (monotonic), **gauge** (up and down), **histogram** (latency buckets), **summary** (quantiles).

**Convention:** `_total`, `_seconds`, `_bytes` suffixes per OpenMetrics.

@section: Scraping and relabeling

The scrape job defines `scrape_interval`, `metrics_path`, and authentication. **Relabel configs** change labels before ingestion (useful to normalize Kubernetes targets).

**`honor_labels`:** when the target already exposes labels that must be preserved.

@section: PromQL basics

* `rate(http_requests_total[5m])` — requests per second from counters.
* `histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))` — latency p99 (typical histogram pattern).

**Aggregations:** `sum(...) by (job)`, `avg_over_time`, etc.

**Avoid** expensive queries on huge ranges without an appropriate step.

@section: Recording and alerting rules

**Recording rules** precompute heavy series for faster dashboards.

**Alerting rules** evaluate PromQL expressions and send to Alertmanager:

```yaml
groups:
  - name: example
    rules:
      - alert: HighErrorRate
        expr: sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) > 0.05
        for: 5m
        labels:
          severity: page
        annotations:
          summary: "Error rate > 5%"
```

`for` avoids paging on brief spikes.

@section: Cardinality

Each unique label combination is a new series. **High cardinality** (user_id, email, full URL) can take down Prometheus.

**Good practices:**

* Limit dynamic labels in applications.
* Use `metric_relabel_configs` to drop abusive series.

@section: Application instrumentation

Official libraries for Go, Java, Python, etc. Expose `/metrics` on an internal port and protect it on the network. **RED/USE** mnemonics describe what to measure (rate, errors, duration / utilization, saturation, errors).

@section: Common mistakes

* Using `rate()` on gauges.
* Alerts without `for` that fire on every tiny network blip.
* Short Prometheus retention without a long-term layer (no year-long trends).

@section: Suggested lab

1. Run an example exporter (node_exporter) locally or in a container.
2. Configure static scrape and explore metrics in the Prometheus UI.
3. Write a `rate` query on a counter and a test alert with a high threshold.

@quiz: Why does Prometheus primarily use a pull model instead of push?
@option: Push is illegal in Kubernetes
@correct: Centralized scrape control, target discovery, and clear collector health semantics
@option: Pull eliminates the need for labels
