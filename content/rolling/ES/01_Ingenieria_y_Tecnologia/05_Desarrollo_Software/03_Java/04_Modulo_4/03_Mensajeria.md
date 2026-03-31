@title: Mensajería: colas, Kafka y entrega al menos una vez
@icon: 📨
@description: Offsets, idempotencia, duplicados.
@order: 3

# Mensajería: colas, Kafka y entrega al menos una vez

Los brokers como **Kafka** retienen logs particionados con **offsets**. Los consumidores avanzan offsets; un fallo puede reprocesar mensajes → **al menos una vez** implica duplicados posibles salvo **idempotencia** en el consumidor.

@section: Diseño

Claves de partición coherentes preservan orden por entidad de negocio.
@quiz: ¿Qué garantía de entrega suele implicar reintentos y posibles duplicados?
@option: Exactamente una vez siempre gratis
@correct: Al menos una vez (at-least-once)
@option: Solo broadcast UDP
