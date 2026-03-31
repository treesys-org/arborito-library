@title: Seguridad en capa 2: DHCP snooping, DAI y 802.1X
@icon: 🛡️
@description: Ataques de suplantación, mitigaciones en switch y control de acceso a puerto.
@order: 5

# Seguridad en capa 2: cuando el atacante está en la misma LAN

En la LAN, **ARP spoofing**, **DHCP rogue**, **MAC flooding** y **VLAN hopping** son clásicos. Los switches modernos ofrecen **DHCP snooping**, **DAI**, **IP source guard** y **802.1X** para contener abusos. Esta lección resume amenazas y mitigaciones.

@section: DHCP snooping

Marca puertos **trusted** (hacia servidor DHCP) vs **untrusted**. Descarta **OFFER/ACK** en untrusted; construye **binding table** (MAC/IP/VLAN/puerto).

**IP Source Guard** usa esa tabla para filtrar tráfico IP.

@section: DAI (Dynamic ARP Inspection)

Valida ARP contra la tabla de DHCP snooping o ACLs estáticas; bloquea **ARP spoofing** común.

**Rate limiting** para evitar inundaciones.

@section: 802.1X

**Control de acceso a puerto** con autenticación EAP: **supplicant** en el host, **authenticator** en el switch, **RADIUS** al backend.

**MAB** (MAC Authentication Bypass) para dispositivos sin 802.1X (impresoras).

@section: Storm control

Limita **broadcast/multicast/unknown unicast** por porcentaje o pps.

@section: Errores frecuentes

* DHCP snooping sin **trusted** correcto → clientes sin IP.
* 802.1X en modo estricto sin **guest VLAN** para emergencias.

@section: Laboratorio sugerido

1. Habilita DHCP snooping en un VLAN de prueba y observa bindings.
2. Simula un servidor DHCP rogue y verifica descarte.
3. Documenta un plan de despliegue 802.1X por fases.

@quiz: ¿Qué ataque mitiga principalmente DHCP snooping?
@option: DDoS a capa 7
@correct: Servidores DHCP no autorizados en la LAN
@option: DNS exfiltration
