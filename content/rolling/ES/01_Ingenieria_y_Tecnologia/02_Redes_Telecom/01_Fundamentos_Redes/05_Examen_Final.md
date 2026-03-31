
@title: Examen de certificación: Fundamentos de redes
@exam
@icon: 🌐
@description: Modelos OSI/TCP, switching, routing y políticas. Aprueba la mayoría para certificar el curso.
@order: 5

# Examen final: Fundamentos de redes

Cuatro bloques: capas bajas y direccionamiento, switching, routing y servicios avanzados.

> **Instrucciones:** Una respuesta correcta por pregunta; mayoría para aprobar.


## Bloque 1: Modelos, Ethernet e IP (módulo 1)

@quiz: ¿En qué capa del modelo OSI operan típicamente los switches de capa 2 “puros”?
@option: Capa 3.
@correct: Capa 2 (enlace de datos).
@option: Capa 7 exclusivamente.
@option: Capa 1 únicamente con fibra.

@quiz: ¿Qué PDU asocia habitualmente la capa de transporte en el modelo OSI?
@option: Trama (frame).
@correct: Segmento / datagrama según protocolo (p. ej. TCP segment).
@option: Paquete IP siempre.
@option: Bit crudo sin estructura.

@quiz: ¿Qué campo de 6 bytes identifica un nodo en Ethernet?
@option: IPv6 interface ID solo.
@correct: Dirección MAC.
@option: Número AS.
@option: VLAN ID.

@quiz: ¿Qué máscara /24 implica en IPv4?
@option: 32 bits de prefijo de red.
@correct: 24 bits de prefijo (256 direcciones host teóricas en el bloque).
@option: Solo hosts DHCP.
@option: Ausencia de subredes.

@quiz: ¿Qué protocolo de resolución traduce IPv4 conocido a MAC en LAN?
@option: DNS.
@correct: ARP.
@option: ICMP solo.
@option: OSPF.

@quiz: Una dirección IPv6 link-local suele:
@option: Ser enrutable globalmente por defecto.
@correct: Permanecer en el enlace local (alcance limitado).
@option: Reemplazar MAC en capa 2.
@option: Exigir NAT444 obligatorio.


## Bloque 2: Switching y capa 2 (módulo 2)

@quiz: ¿Qué función principal cumple una VLAN en un switch?
@option: Cifrar tráfico WAN automáticamente.
@correct: Segmentar dominios de broadcast lógicamente en el mismo equipo físico.
@option: Sustituir direccionamiento IP.
@option: Aumentar MTU de capa 3.

@quiz: STP (802.1D) busca principalmente:
@option: Maximizar loops de capa 2 sin control.
@correct: Evitar bucles de switching calculando un árbol que bloquea puertos redundantes.
@option: Traducir IPv4 a IPv6.
@option: Balancear carga L3 con BGP.

@quiz: EtherChannel agrupa enlaces para:
@option: Reducir MTU únicamente.
@correct: Aumentar ancho de banda agregado y redundancia entre dos switches.
@option: Eliminar la necesidad de VLANs.
@option: Forzar half-duplex.

@quiz: Un ataque de MAC flooding intenta:
@option: Cifrar STP.
@correct: Llenar la tabla CAM para forzar comportamiento tipo hub o saturación.
@option: Cambiar prefijos OSPF.
@option: Deshabilitar ARP legítimo en WAN.

@quiz: El modo *trunk* en un puerto de switch transporta típicamente:
@option: Solo una VLAN sin etiquetar siempre.
@correct: Múltiples VLAN mediante etiquetado (p. ej. 802.1Q).
@option: Solo tráfico IPv6.
@option: Tramas sin FCS.

@quiz: Port security en switch puede limitar:
@option: Solo ancho de banda WAN.
@correct: Número o identidad de MAC aprendidas en un puerto de acceso.
@option: Tamaño de rutas BGP.
@option: Prioridad DSCP global.


## Bloque 3: Routing (módulo 3)

@quiz: Una ruta estática predeterminada (0.0.0.0/0) actúa como:
@option: Resumen solo dentro de OSPF.
@correct: Gateway of last resort para destinos no más específicos.
@option: ACL implícita.
@option: Dirección multicast obligatoria.

@quiz: OSPF es un protocolo de routing:
@option: Vector distancia puro clásico sin LSAs.
@correct: Estado de enlace dentro de un AS (intra-domain típico).
@option: Exclusivamente de política inter-AS únicamente.
@option: De capa 2 para STP.

@quiz: FHRP como HSRP/VRRP provee:
@option: Cifrado punto a punto IPsec obligatorio.
@correct: Gateway virtual redundante ante fallo del router activo.
@option: NAT overload exclusivo.
@option: Sustituto de DNS.

@quiz: PAT/NAT sobrecargado permite:
@option: Infinitas conexiones sin tabla de estados.
@correct: Multiplexar muchas sesiones internas hacia pocas direcciones públicas usando puertos.
@option: Eliminar necesidad de routing.
@option: Solo traducir IPv6 a MAC.

@quiz: El router usa la entrada de tabla cuya:
@option: Máscara sea siempre /0.
@correct: Prefijo sea el más largo que coincida (longest prefix match).
@option: Métrica administrativa sea la mayor.
@option: Primer octeto sea par.

@quiz: Distancia administrativa más baja en Cisco-like implica:
@option: Ruta menos confiable.
@correct: Mayor preferencia frente a otras fuentes para el mismo prefijo.
@option: Solo rutas estáticas prohibidas.
@option: Empate automático con BGP externo.


## Bloque 4: Políticas, QoS y automatización (módulo 4)

@quiz: Una ACL extendida IPv4 filtra típicamente por:
@option: Solo números de AS BGP.
@correct: Direcciones/puertos/protocolo según reglas configuradas.
@option: Solo VLAN nativa.
@option: Solo nombres DNS dinámicos.

@quiz: Un servidor DNS autoritativo para `ejemplo.com`:
@option: Solo hace caching recursivo para clientes stub.
@correct: Mantiene las RR de la zona y responde con autoridad para ese dominio.
@option: Reemplaza al resolver recursivo siempre.
@option: Opera solo en capa 2.

@quiz: QoS en redes IP suele usar marcas como:
@option: Solo MAC OUI.
@correct: DSCP / CoS en distintos puntos del trayecto.
@option: Solo VLAN 0.
@option: TTL únicamente.

@quiz: SDN separa conceptualmente:
@option: Capa 1 y 2 únicamente.
@correct: Plano de control del plano de datos (centralización de decisiones).
@option: IPv4 e IPv6 en hosts.
@option: Switching y cableado físico idénticos.

@quiz: Automatizar configuración de red con plantillas (Ansible, etc.) busca:
@option: Eliminar la necesidad de modelos OSI.
@correct: Idempotencia, repetibilidad y menos error humano.
@option: Reemplazar direccionamiento IP.
@option: Prohibir SNMP siempre.

@quiz: ICMP puede usarse para:
@option: Transportar datos de aplicación voluminosos.
@correct: Mensajes de control como inalcanzable o echo request/reply (ping).
@option: Sustituir TCP en HTTP.
@option: Cifrar sesiones TLS.
