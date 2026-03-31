@title: Prometheus: modelo de datos, scrape y PromQL
@icon: 📈
@description: Métricas time-series, jobs, labels, alertas y buenas prácticas de cardinalidad.
@order: 2

# Prometheus: métricas para sistemas cloud-native

**Prometheus** es un sistema de monitorización y alertas orientado a **series temporales**. Recopila métricas vía **pull** HTTP (`/metrics`) de targets descubiertos (Kubernetes, exporters, tu aplicación). Esta lección cubre el modelo de datos, **PromQL**, alertas y trampas de **cardinalidad**.

@section: Arquitectura breve

* **Prometheus server:** scrape, almacenamiento local (TSDB), evaluación de reglas.
* **Service discovery:** Kubernetes, Consul, DNS, ficheros estáticos.
* **Alertmanager:** enruta, deduplica, agrupa y notifica alertas (PagerDuty, Slack, email).
* **Pushgateway:** excepción para jobs batch que no pueden ser scrapeados (usar con cuidado).

**Federación** y **Thanos/Cortex/Mimir** escalan lectura y retención en entornos grandes.

@section: Modelo de datos

Cada muestra es una **serie** identificada por:

* **Nombre de métrica** (`http_requests_total`).
* **Labels** clave-valor (`method="GET"`, `status="200"`).

**Tipos** habituales en client libraries: **counter** (solo sube), **gauge** (sube y baja), **histogram** (buckets de latencia), **summary** (cuantiles).

**Convención:** sufijos `_total`, `_seconds`, `_bytes` según OpenMetrics.

@section: Scrape y relabeling

El job de scrape define `scrape_interval`, `metrics_path` y autenticación. **Relabel configs** modifican labels antes de ingestión (útil para normalizar targets de Kubernetes).

**`honor_labels`:** si el target ya expone labels que deben preservarse.

@section: PromQL básico

* `rate(http_requests_total[5m])` — peticiones por segundo a partir de contadores.
* `histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))` — p99 de latencia (patrón típico con histogramas).

**Agregaciones:** `sum(...) by (job)`, `avg_over_time`, etc.

**Evita** queries caras en dashboards con rangos enormes sin step adecuado.

@section: Reglas de grabación y alertas

**Recording rules** precomputan series pesadas para acelerar dashboards.

**Alerting rules** evalúan expresiones PromQL y envían a Alertmanager:

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

`for` evita páginas por picos breves.

@section: Cardinalidad

Cada combinación única de labels es una serie nueva. **Alta cardinalidad** (user_id, email, URL completa) puede tumbar Prometheus.

**Buenas prácticas:**

* Limita labels dinámicos en aplicaciones.
* Usa `metric_relabel_configs` para descartar series abusivas.

@section: Instrumentación de aplicaciones

Librerías oficiales para Go, Java, Python, etc. Expón `/metrics` en puerto interno y protégelo en red. **RED/USE** son mnemotécnicos para qué medir (rate, errors, duration / utilization, saturation, errors).

@section: Errores frecuentes

* Usar `rate()` en gauges.
* Alertas sin `for` que disparan en cada microcorte de red.
* Retención corta en Prometheus sin capa de largo plazo (no ves tendencias anuales).

@section: Laboratorio sugerido

1. Ejecuta un exporter de ejemplo (node_exporter) en local o contenedor.
2. Configura scrape estático y explora métricas en la UI de Prometheus.
3. Escribe una query `rate` sobre un contador y una alerta de prueba con umbral alto.

@quiz: ¿Por qué Prometheus usa principalmente el modelo pull en lugar de push?
@option: Porque push es ilegal en Kubernetes
@correct: Control centralizado de scrape, descubrimiento de targets y semántica clara de salud del colector
@option: Pull elimina la necesidad de labels
