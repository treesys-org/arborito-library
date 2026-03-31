@title: Observabilidad: Actuator, Micrometer y logs estructurados
@icon: 📈
@description: Health checks, métricas Prometheus, correlación traceId.
@order: 5

# Observabilidad: Actuator, Micrometer y logs estructurados

**Spring Boot Actuator** expone endpoints como `/actuator/health` y `/actuator/metrics`. **Micrometer** exporta a **Prometheus**, Datadog, etc.

En producción, **no abras** endpoints sensibles sin autenticación; usa **network policies** o **reverse proxy** con auth.

Correlación: propaga **trace IDs** en logs (MDC) y headers HTTP para seguir solicitudes a través de servicios.
@quiz: ¿Qué starter añade endpoints operativos tipo health en Spring Boot?
@option: spring-boot-starter-json
@correct: spring-boot-starter-actuator
@option: spring-boot-starter-aop
