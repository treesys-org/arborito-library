@title: Configuración de switch: VLAN 1, gestión y puertos de acceso
@icon: 🖧
@description: IOS/NX-OS básico, VLAN de gestión, troncal 802.1Q y buenas prácticas.
@order: 1

# Configuración de switch L2: base operativa

Los **switches** gestionan dominios de colisión y **VLANs** para segmentar broadcast. Esta lección cubre **puertos de acceso**, **trunk 802.1Q**, **VLAN de gestión** y **buenas prácticas** de administración (SSH, AAA, no usar VLAN1 para datos sensibles).

@section: Modos de puerto

* **Access:** un solo VLAN de usuario; tramas **untagged** en el host, con **PVID** en el switch.
* **Trunk:** transporta múltiples VLANs con **tagging 802.1Q**; **native VLAN** sin tag (debe coincidir en ambos extremos).

**DTP** (Cisco) puede negociar trunk; en entornos seguros suele deshabilitarse para evitar sorpresas.

@section: VLAN de gestión

Asigna **SVI** (VLAN interface) o equivalente con IP para **SSH/SNMP**. Separa **planos** de gestión y datos cuando sea posible (out-of-band, VRF ligero).

**Gateway** de gestión debe ser alcanzable por rutas de administración.

@section: Spanning tree (adelanto)

Antes de crear loops físicos, **STP** debe estar activo. Un error en trunking puede crear **loops** si conectas dos switches con dos cables sin **EtherChannel** ni STP.

@section: Seguridad básica

* **BPDU Guard** en puertos de acceso.
* **Port-security** para limitar MACs en puestos de usuario.
* **SSH** v2, no telnet.

@section: Errores frecuentes

* **Native VLAN** distinta en ambos extremos del trunk → **VLAN hopping** o tráfico cruzado.
* Management en la misma VLAN que usuarios sin ACLs.

@section: Laboratorio sugerido

1. Configura dos VLANs (datos/VoIP) y un trunk entre dos switches de laboratorio.
2. Verifica reachability con ping entre VLANs vía router-on-a-stick o L3 switch.
3. Muestra tablas MAC y spanning-tree.

@quiz: ¿Qué es un puerto trunk en Ethernet conmutada?
@option: Puerto sin cable
@correct: Puerto que transporta múltiples VLANs etiquetadas 802.1Q y define una VLAN nativa
@option: Puerto solo de 10 Mbps
