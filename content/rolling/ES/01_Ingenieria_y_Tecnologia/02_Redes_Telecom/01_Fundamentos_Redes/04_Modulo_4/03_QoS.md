@title: QoS: colas, marcado y congestión
@icon: 📶
@description: DSCP/CoS, shaping vs policing, LLQ y QoS en WAN.
@order: 3

# QoS: cuando el ancho de banda no alcanza

**QoS** gestiona **congestión** marcando tráfico (**DSCP**, **CoS 802.1p**), asignando **colas** (CBWFQ, **LLQ** para voz), y aplicando **policing/shaping**. Esta lección evita mitos: QoS **no crea** ancho de banda, **prioriza** y limita.

@section: Marcado

* **Layer 2:** `802.1p` en tag VLAN (3 bits).
* **Layer 3:** **DSCP** en IP ToS (clases EF, AFxy).

**Trust boundary** en el switch de acceso: confía marcas del teléfono IP o reescribe.

@section: Congestión y colas

**Tail drop** es el comportamiento por defecto brutal. **WRED** reduce síncronización TCP. **LLQ** da prioridad estricta a voz con **policer** integrado para no matar el resto.

@section: Policing vs shaping

* **Policing:** descarta o remarca exceso (borde).
* **Shaping:** retiene paquetes en cola para ajustarse a velocidad contratada (útil hacia WAN).

@section: WAN

**LLQ + CBWFQ** en routers de sucursal; alinea con **provider QoS** (MPLS classes) si existe **mapping** acordado.

@section: Errores frecuentes

* Marcar todo como **EF** → colapsa la cola de voz real.
* No alinear **DSCP end-to-end** (marcas pierden en algún salto).

@section: Laboratorio sugerido

1. Marca ICMP y tráfico UDP con `DSCP` distinto en Linux (`tc`).
2. Aplica `HTB`/`HFSC` en lab y mide latencia bajo carga.
3. Documenta mapa de clases para una WAN ficticia.

@quiz: ¿Qué describe mejor al LLQ en políticas QoS?
@option: Cola sin prioridad
@correct: Cola de baja latencia/prioridad estricta para tráfico sensible (p.ej. voz) con límite para no inanición del resto
@option: Cifrado de paquetes
