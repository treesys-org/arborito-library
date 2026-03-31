@title: Messaging and Kafka basics
@icon: 📨
@description: Offsets, idempotent consumers.
@order: 3

# Messaging and Kafka basics

Partitioned logs with **offsets** enable scalable consumption. **At-least-once** delivery may duplicate messages—make consumers **idempotent**.
@quiz: Which delivery guarantee often implies retries and duplicates?
@option: Exactly-once for free
@correct: At-least-once
@option: UDP broadcast only
