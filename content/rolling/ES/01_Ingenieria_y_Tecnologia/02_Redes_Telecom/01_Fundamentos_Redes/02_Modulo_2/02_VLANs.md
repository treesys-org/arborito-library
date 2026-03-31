@title: VLANs: segmentación y diseño de capa 2
@icon: 🎭
@description: Broadcast domains, 802.1Q, inter-VLAN routing y troubleshooting.
@order: 2

# VLANs: dominios de broadcast bajo control

Una **VLAN** (`802.1Q`) segmenta **dominios de broadcast** lógicamente sobre la misma infra física. Diseñar VLANs bien reduce **tormentas broadcast**, aísla **VoIP** y **invitados**, y prepara **QoS** por cola. Esta lección cubre tagging, **inter-VLAN routing** y **errores típicos**.

@section: 802.1Q tagging

Cada trama puede llevar **Tag** con **VLAN ID** (12 bits). El host normalmente envía **untagged**; el switch añade PVID en el puerto de acceso.

**Q-in-Q** (stacked VLAN) en proveedores: doble tag; útil en carrier, no en acceso típico.

@section: Inter-VLAN routing

Para comunicar VLANs necesitas **router** o **L3 switch** (SVI). Sin eso, hosts en VLANs distintas no se hablan aunque compartan cable físico en otros contextos.

**Router-on-a-stick:** subinterfaces 802.1Q en un router. **L3 switch:** routing directo en hardware.

@section: VLANs especiales

* **Black hole** VLAN para tráfico descartado (no usar VLAN1 por defecto).
* **Voice VLAN** con CDP/LLDP para teléfonos IP (según vendor).

@section: Troubleshooting

* Host en VLAN incorrecta → sin DHCP o gateway erróneo.
* **Trunk** permitiendo solo algunas VLANs (`allowed vlan`) → tráfico filtrado.

@section: Errores frecuentes

* **VLAN database** sin documentar en entornos multi-equipo.
* Cambiar **native VLAN** sin actualizar todos los extremos.

@section: Laboratorio sugerido

1. Crea VLAN 10 y 20; asigna hosts a cada una.
2. Configura SVI o router-on-a-stick y valida ping cruzado.
3. Filtra con `vlan allowed` en trunk y observa el fallo.

@quiz: ¿Qué problema resuelve principalmente el uso de VLANs?
@option: Aumentar la velocidad de Ethernet
@correct: Segmentar dominios de broadcast y políticas de capa 2
@option: Eliminar la necesidad de IP
