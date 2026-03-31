@title: Distributed Tracing: OpenTelemetry, Tempo, and Jaeger
@icon: 🔭
@description: Spans, W3C context, sampling, and operational cost.
@order: 5

# Distributed tracing: see the full path of a request

A **trace** records a request’s journey across **multiple services**: each piece is a **span** with duration, errors, and metadata. Without traces, debugging latency in microservice architectures is guessing which hop failed. This lesson introduces **OpenTelemetry**, context propagation, backends (**Jaeger**, **Tempo**), and **sampling**.

@section: Spans and traces

* **Trace ID** identifies the end-to-end request.
* **Span ID** identifies work inside a service.
* Spans form a **tree** (parent/child) reflecting nested calls.

Standard attributes (HTTP method, status, sanitized DB statements) help filtering without reinventing names.

@section: OpenTelemetry (OTel)

**OpenTelemetry** unifies **instrumentation**, **export**, and **semantics** for traces, metrics, and logs. SDKs for Java, Go, Python, .NET, etc.

The **Collector** can receive OTLP, transform, and export to Jaeger, Tempo, or cloud vendors.

Benefit: reduce APM lock-in and standardize attributes.

@section: Context propagation

Services must forward **trace context** in headers (**W3C Trace Context**: `traceparent`). If a hop drops context, the trace **breaks** into disconnected fragments.

**Gateways** and **load balancers** must preserve or generate context per design.

@section: Backends: Jaeger and Grafana Tempo

* **Jaeger:** classic trace UI; pluggable storage (Elasticsearch, Cassandra, badger).
* **Tempo:** trace store optimized for **object storage**; strong Grafana integration (correlation with Loki/Prometheus).

Choose based on scale, cost, and existing ecosystem.

@section: Sampling

Recording 100% of traffic in large systems is expensive. **Head sampling** decides at the start; **tail sampling** (more complex) keeps interesting traces after completion (errors, slowness).

Bad sampling policies hide incidents or explode cost.

@section: Relationship to logs and metrics

**Exemplars** in Prometheus link series to trace IDs. **Logs** with `trace_id` jump from an error to the full trace. **Three pillars** together reduce MTTR.

@section: Security and privacy

Do not record full HTTP bodies with PII. **Sanitize** SQL queries and parameters. Control who can read production traces (audit).

@section: Common mistakes

* Instrumenting only the edge API and forgetting databases or internal queues (gaps in the trace).
* Sampling too aggressively when you need fine diagnosis.
* Incompatible exporter and backend OTLP versions.

@section: Suggested lab

1. Run Jaeger **all-in-one** in a container.
2. Instrument a sample app with OTel (official tutorials) and generate traces.
3. Find the slowest span and note which attributes would help in a real incident.

@quiz: What problem does W3C Trace Context propagation mainly solve?
@option: Encrypting HTTP traffic
@correct: Linking spans from different services under the same trace_id
@option: Compressing logs
