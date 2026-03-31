@title: Logging centralizado: Loki, ELK y correlación
@icon: 📜
@description: Agregación, índices, retención y trazas con métricas.
@order: 4

# Logging centralizado: buscar agujas sin tumbar el cluster

Los **logs** son líneas de texto (o JSON) emitidas por aplicaciones y sistema operativo. En decenas de máquinas o miles de pods, **grep en SSH** no escala: necesitas **agregación**, **búsqueda** y **retención** con políticas claras. Esta lección contrasta enfoques (ELK, Loki) y conecta logs con métricas y trazas.

@section: Qué es un pipeline de logs

1. **Recolección:** agente en nodo (Fluent Bit, Fluentd, Promtail) o sidecar.
2. **Transporte:** Kafka, HTTP, syslog.
3. **Ingesta e indexación:** Elasticsearch, OpenSearch, Loki, cloud vendors.
4. **Consulta:** Kibana, Grafana Explore, interfaces propietarias.

El coste suele estar en **almacenamiento** y **índices**; por eso la **cardinalidad** de campos importa tanto como en métricas.

@section: ELK / OpenSearch

**Elasticsearch** indexa texto completo con analizadores potentes. **Kibana** visualiza y crea dashboards. **OpenSearch** es fork open source compatible.

Fortalezas: búsquedas complejas, agregaciones. Debilidades: **operar clusters** grandes es un trabajo a tiempo completo; coste de disco e índices mal gestionados.

@section: Grafana Loki

**Loki** etiqueta logs como Prometheus (labels) y almacena el cuerpo comprimido en **object storage** (S3, GCS). **LogQL** mezcla filtros de labels y búsqueda de contenido.

Ventaja: coste y operación más simples alineados con ecosistema Prometheus. Limitación: no es un motor de búsqueda full-text tan rico como Elasticsearch para todos los casos.

@section: Formato estructurado

**JSON** estructurado (`{"level":"error","trace_id":"..."}`) facilita filtros y correlación. Incluye **trace_id** y **span_id** si usas tracing distribuido.

**Niveles** (`INFO`, `WARN`, `ERROR`) coherentes evitan ruido en alertas.

@section: Retención y muestreo

Define **retención** por tipo de log (aplicación vs. auditoría). Algunos equipos **muestrean** logs de acceso de alto volumen y guardan el 100% solo en incidentes.

**PII:** enmascara o tokeniza datos personales antes de enviar a sistemas compartidos.

@section: Correlación con métricas y trazas

Desde Grafana: salta de un pico en Prometheus a logs filtrados por `pod` o `trace_id`. **Exemplars** en Prometheus enlazan series con trace IDs en algunos setups.

@section: Errores frecuentes

* Loggear secretos o tokens (filtración en agregador).
* Campos de alta cardinalidad en índices Elasticsearch (explosión de shards).
* Sin política de rotación → disco lleno y caída del cluster.

@section: Laboratorio sugerido

1. Despliega Promtail + Loki + Grafana en local (docker-compose oficial de Grafana).
2. Envía logs JSON desde una app de prueba y explora en **Explore** con LogQL.
3. Escribe una regla de alerta sobre tasa de logs `level=error` (si tu stack lo permite).

@quiz: ¿Qué diferencia principal suele existir entre Loki y Elasticsearch para logs?
@option: Loki no puede etiquetar logs
@correct: Loki indexa fuerte por labels (estilo Prometheus) y empuja el texto a almacenamiento de objetos; Elasticsearch indexa texto completo de forma más pesada
@option: Elasticsearch solo funciona en Windows
