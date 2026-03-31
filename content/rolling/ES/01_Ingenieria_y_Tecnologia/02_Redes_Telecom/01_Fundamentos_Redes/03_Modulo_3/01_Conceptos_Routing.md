@title: Conceptos de routing: tablas, longest match y métricas
@icon: 🧭
@description: Next hop, AD, protocolos conectados vs dinámicos y resumen de rutas.
@order: 1

# Routing IP: cómo el router decide el siguiente salto

El **routing** determina **hacia dónde** reenviar paquetes IP no locales. La decisión usa **tabla de rutas**, **longest prefix match**, **distancia administrativa** y **métricas** del protocolo dinámico. Esta lección evita confusiones entre **default route**, **host routes** y **null0**.

@section: Longest prefix match

Si existen `10.0.0.0/8` y `10.1.0.0/16`, el tráfico hacia `10.1.0.5` elige **/16** por ser más específico.

**Default route** `0.0.0.0/0` es la última opción.

@section: Distancia administrativa (AD)

Cuando **dos fuentes** anuncian la misma ruta, el **AD** más bajo gana (estático vs OSPF vs eBGP según vendor). **Métrica** decide dentro del mismo protocolo.

**Floating static** con AD mayor para backup.

@section: Conectadas y estáticas

**Directly connected** tras configurar IP en interfaz; **estáticas** para rutas fijas. **Recursive next-hop** si apuntas a IP sin salida explícita.

**Null routing** descarta tráfico (blackhole) para mitigar ataques o agregados.

@section: Balanceo y ECMP

**ECMP** reparte flujos por múltiples next-hops de igual coste; puede ser **per-packet** o **per-hash** según implementación.

@section: Errores frecuentes

* **Loop** de rutas estáticas bidireccionales mal diseñadas.
* Asimetría de rutas que rompe **stateful firewalls** sin cuidado.

@section: Laboratorio sugerido

1. Crea tres rutas hacia el mismo destino con prefijos distintos y observa cuál gana.
2. Configura estática flotante con AD distinta y corta el primario.
3. Usa `traceroute` para verificar camino.

@quiz: ¿Qué regla usa un router para elegir entre dos rutas que coinciden con el destino?
@option: La mayor métrica
@correct: Longest prefix match (prefijo más largo / más específico)
@option: La primera en la lista alfabética
