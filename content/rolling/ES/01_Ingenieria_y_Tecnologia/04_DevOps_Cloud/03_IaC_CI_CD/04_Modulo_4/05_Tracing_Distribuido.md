@title: Trazas distribuidas: OpenTelemetry, Tempo y Jaeger
@icon: 🔭
@description: Spans, contexto W3C, muestreo y coste operativo.
@order: 5

# Trazas distribuidas: ver el camino completo de una petición

Una **traza** registra el viaje de una solicitud a través de **múltiples servicios**: cada fragmento es un **span** con duración, errores y metadata. Sin trazas, depurar latencia en arquitecturas de microservicios es adivinar qué hop falló. Esta lección introduce **OpenTelemetry**, propagación de contexto, backends (**Jaeger**, **Tempo**) y **muestreo**.

@section: Spans y trazas

* **Trace ID** identifica la solicitud end-to-end.
* **Span ID** identifica un trabajo dentro de un servicio.
* Los spans forman un **árbol** (parent/child) reflejando llamadas anidadas.

Atributos estándar (HTTP method, status, DB statement sanitizado) ayudan a filtrar sin reinventar nombres.

@section: OpenTelemetry (OTel)

**OpenTelemetry** unifica **instrumentación**, **exportación** y **semántica** para trazas, métricas y logs. SDKs para Java, Go, Python, .NET, etc.

**Collector** puede recibir OTLP, transformar y exportar a Jaeger, Tempo, vendors cloud.

Beneficio: evitar lock-in de un solo APM y estandarizar atributos.

@section: Propagación de contexto

Los servicios deben reenviar **trace context** en headers (estándar **W3C Trace Context**: `traceparent`). Si un hop pierde el contexto, la traza se **rompe** y ves fragmentos inconexos.

**Gateways** y **balanceadores** deben preservar o generar contexto según el diseño.

@section: Backends: Jaeger y Grafana Tempo

* **Jaeger:** UI clásica para buscar trazas; almacenamiento pluggable (Elasticsearch, Cassandra, badger).
* **Tempo:** almacén de trazas optimizado para **object storage**; integración fuerte con Grafana (correlación con Loki/Prometheus).

Elegir según escala, coste y ecosistema ya desplegado.

@section: Muestreo

Registrar el 100% del tráfico en sistemas grandes es caro. **Head sampling** decide al inicio; **tail sampling** (más complejo) retiene trazas interesantes tras completarse (errores, lentitud).

Políticas de muestreo mal configuradas ocultan incidentes o explotan coste.

@section: Relación con logs y métricas

**Exemplars** en Prometheus enlazan series con trace IDs. **Logs** con `trace_id` permiten saltar de un error a la traza completa. **Tres pilares** juntos reducen MTTR.

@section: Seguridad y privacidad

No grabes cuerpos HTTP completos con PII. **Sanitiza** consultas SQL y parámetros. Controla quién puede leer trazas de producción (auditoría).

@section: Errores frecuentes

* Instrumentar solo el edge API y olvidar bases de datos o colas internas (huecos en la traza).
* Muestreo demasiado agresivo en entornos donde necesitas diagnóstico fino.
* Versiones incompatibles de exporters y backend OTLP.

@section: Laboratorio sugerido

1. Ejecuta Jaeger **all-in-one** en contenedor.
2. Instrumenta una app de ejemplo con OTel (hay tutoriales oficiales) y genera trazas.
3. Encuentra el span más lento y anota qué atributos te ayudarían en un incidente real.

@quiz: ¿Qué problema resuelve principalmente la propagación W3C Trace Context?
@option: Cifrar tráfico HTTP
@correct: Enlazar spans de servicios distintos bajo el mismo trace_id
@option: Comprimir logs
