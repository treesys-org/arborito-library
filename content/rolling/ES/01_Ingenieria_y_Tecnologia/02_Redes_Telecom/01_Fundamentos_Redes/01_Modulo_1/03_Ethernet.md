@title: Ethernet: tramas, MAC y conmutación
@icon: 📡
@description: Formato de trama, dominios de colisión vs broadcast, switching y MTU.
@order: 3

# Ethernet: de la trama al switch

**Ethernet** es la familia dominante en LAN. Entender **tramas**, direcciones **MAC**, **conmutación** capa 2 y **MTU** evita confusiones cuando mezclas **VLAN**, **LAG** y **jumbo frames**. Esta lección es el puente entre cableado físico y **IPv4** que viene después.

@section: Trama Ethernet (visión simplificada)

Campos clave: **MAC destino/origen**, **EtherType/Length**, carga útil, **FCS** (CRC).  
**MTU** típico 1500 bytes en Ethernet clásico; **jumbo** (9000) solo si **todo** el camino lo soporta.

@section: Direcciones MAC

48 bits, **unicast** vs **multicast** (p.ej. STP usa 01:80:c2:…). El bit **U/L** y **I/G** importan al interpretar trazas.

**Tabla CAM** del switch: aprende MAC por puerto; **flooding** si desconocida (dentro del dominio broadcast de esa VLAN).

@section: Switching vs hubs

Los **switches** modernos eliminan **dominios de colisión** por puerto (full duplex). Los hubs (obsoletos) compartían medio → colisiones.

**Dominio de broadcast:** sigue siendo por **VLAN** (y por todo el L2 si no segmentas).

@section: Autonegociación

**Speed/duplex** se negocian; **mismatch** manual vs auto genera **colisiones** o rendimiento pobre. En enlaces críticos documenta ambos extremos.

@section: Control de flujo (opcional)

**802.3x PAUSE** puede detener temporalmente el emisor; mal usado puede causar **head-of-line blocking** en ciertos diseños. Muchos data centers lo deshabilitan y gestionan congestión arriba.

@section: Errores frecuentes

* **Loop** de capa 2 sin STP → tormenta broadcast.
* **MTU** inconsistente → fragmentación IP o black holes PMTUD.
* Confundir **MAC flapping** (cable duplicado / loop) con problemas de routing.

@section: Laboratorio sugerido

1. En un switch de laboratorio, observa **MAC table** antes y después de ping entre dos hosts.
2. Cambia MTU en Linux (`ip link set dev eth0 mtu …`) y mide comportamiento con ping **no fragmentar**.
3. Captura tráfico ARP y relaciona con resolución de siguiente salto.

@quiz: ¿Qué dispositivo aprende direcciones MAC y asocia puerto?
@option: Router exclusivamente
@correct: Switch de capa 2 (tabla CAM)
@option: Solo el servidor DHCP
