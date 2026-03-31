@title: FHRP: HSRP, VRRP y GLBP
@icon: ⚖️
@description: Gateway redundante, prioridad, preempt y tracking.
@order: 4

# First Hop Redundancy: gateway que no sea SPOF

**HSRP** (Cisco), **VRRP** (estándar IETF), **GLBP** (balanceo activo-activo Cisco) proveen **IP virtual** compartida por routers para que hosts usen **un default gateway** tolerante a fallos. Esta lección cubre **prioridad**, **preempt**, **tracking** y **consideraciones de L2**.

@section: Virtual IP/MAC

Los hosts apuntan a **VIP**; el **master/active** responde ARP. Si cae, otro miembro toma rol tras **hello timers**.

**Preempt:** el router de mayor prioridad recupera rol al volver (evalúa si lo deseas en estabilidad).

@section: Tracking

**Object tracking** (interfaces IP SLA) reduce prioridad si el uplink WAN cae, forzando failover aunque el LAN esté vivo.

@section: Split-brain

Si el **L2** entre routers FHRP se particiona, **dos activos** pueden causar **IP duplicate** o MAC flapping. **BFD** y diseño L2 sólido mitigan.

@section: Comparación rápida

| Proto | Vendor | Balanceo |
|-------|--------|----------|
| HSRP | Cisco | Activo/pasivo típico |
| VRRP | Estándar | Activo/pasivo |
| GLBP | Cisco | Activo/activo por host ARP |

@section: Errores frecuentes

* **Misma prioridad** y MAC virtual conflictiva.
* No alinear **timers** con switches STP convergence.

@section: Laboratorio sugerido

1. Configura HSRP/VRRP entre dos routers L3 en una VLAN.
2. Corta el enlace activo y observa failover con ping continuo.
3. Habilita tracking de interfaz WAN simulada.

@quiz: ¿Qué problema resuelve un protocolo FHRP en la LAN?
@option: NAT de salida
@correct: Redundancia del primer salto (default gateway) ante fallo de router
@option: Cifrado Wi‑Fi
