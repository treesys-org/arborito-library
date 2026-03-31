@title: Microservicios: límites, consistencia y operación
@icon: 🧩
@description: Bounded contexts, sagas, observabilidad.
@order: 2

# Microservicios: límites, consistencia y operación

Los microservicios **despliegan** de forma independiente; el coste es **red distribuida**, **consistencia eventual**, y **operación** más compleja (tracing, logs correlacionados).

Diseña límites alineados al negocio (**DDD**). Evita “nanoservicios” sin beneficio.
@quiz: ¿Qué suele aumentar al partir un monolito en muchos servicios?
@option: Latencia cero
@correct: Complejidad operativa y fallos de red
@option: Imposibilidad de HTTP
