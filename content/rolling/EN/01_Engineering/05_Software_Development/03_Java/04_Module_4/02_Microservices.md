@title: Microservices trade-offs
@icon: 🧩
@description: Boundaries, eventual consistency, ops complexity.
@order: 2

# Microservices trade-offs

Independent deployment trades for **network faults**, **distributed consistency**, and **higher operational load** (tracing, SLOs per service).
@quiz: What typically rises when splitting a monolith into many services?
@option: Zero latency
@correct: Operational complexity and network failures
@option: HTTP becomes impossible
