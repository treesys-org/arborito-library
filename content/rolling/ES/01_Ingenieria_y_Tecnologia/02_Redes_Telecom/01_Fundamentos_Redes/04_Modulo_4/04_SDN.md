@title: SDN: plano de control y datos separados
@icon: ☁️
@description: OpenFlow, controladores, overlays VXLAN y trade-offs operativos.
@order: 4

# SDN: programar la red desde un controlador

**SDN** separa **plano de control** (lógica, políticas) del **plano de datos** (reenvío). Implementaciones van de **OpenFlow** puro a **SD-WAN** y **fabrics** con **VXLAN/EVPN**. Esta lección fija conceptos sin atarte a un solo vendor.

@section: Control vs data plane

Los switches **OpenFlow** consultan al **controlador** para instalar flujos. Ventaja: innovación centralizada; riesgo: **disponibilidad** del controlador y **escala** de tablas TCAM.

@section: Overlays

**VXLAN** encapsula L2 sobre UDP/IP (VNI). **EVPN** distribuye MAC/IP vía BGP en el control plane, popular en **leaf-spine** data centers.

**SD-WAN** usa overlays sobre múltiples transports (MPLS, Internet LTE).

@section: Automatización

**Intent-based networking** declara resultados; el controlador traduce a configuración baja. Requiere **modelos de datos** (YANG) y **APIs** estables.

@section: Trade-offs

* **Vendor lock-in** del controlador.
* **Debugging** más abstracto (túnel vs subyacente).

@section: Errores frecuentes

* **MTU** insuficiente para overlays (fragmentación oculta).
* Subdimensionar **head-end** SD-WAN para cifrado.

@section: Laboratorio sugerido

1. Lee un diagrama **spine-leaf** con EVPN y anota roles BGP.
2. Compara SD-WAN vs MPLS tradicional para un escenario de 20 sucursales.
3. Lista métricas que vigilarías (latencia túnel, pérdida subyacente).

@quiz: ¿Qué encapsulado es común en overlays de data center modernos?
@option: PPPoE
@correct: VXLAN (MAC-in-UDP)
@option: Frame Relay
