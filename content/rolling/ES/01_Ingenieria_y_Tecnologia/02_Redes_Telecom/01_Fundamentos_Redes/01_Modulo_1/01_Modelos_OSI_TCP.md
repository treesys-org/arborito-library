@title: Modelos OSI y TCP/IP: capa por capa
@icon: 📚
@description: Encapsulación, PDU, puertos y mapa mental para diagnosticar fallos.
@order: 1

# Modelos OSI y TCP/IP: el mapa que ordena el caos

Los modelos **OSI** (7 capas) y **TCP/IP** (4 capas prácticas) no son adorno de certificación: son un **vocabulario común** para localizar dónde falla algo: ¿físico? ¿IP? ¿TCP? ¿aplicación? Esta lección fija **encapsulación**, **PDU** y el salto entre modelos sin confundir «capa 4» con «puerto» de forma vaga.

@section: Por qué dos modelos

**OSI** separa finamente (hasta la capa de presentación y sesión). **TCP/IP** agrupa lo que en la práctica implementamos junto (pila del kernel, sockets). En troubleshooting sueles mezclar: «esto huele a **capa 2**» (STP/VLAN) vs «**capa 3**» (rutas) vs «**capa 7**» (HTTP/DNS).

@section: OSI en una tabla operativa

| Capa | Nombre | PDU / unidad | Ejemplos |
|------|--------|--------------|----------|
| 7 | Aplicación | Datos | HTTP, DNS, SSH |
| 4 | Transporte | Segmento | TCP, UDP, puertos |
| 3 | Red | Paquete | IP, ICMP, rutas |
| 2 | Enlace | Trama | Ethernet, MAC, VLAN tag |
| 1 | Físico | Bits | cable, fibra, codificación |

Las capas 5 y 6 en OSI a menudo se absorben en «aplicación» en TCP/IP.

@section: Encapsulación

Cada capa añade **cabeceras** (y a veces trailer) a la carga útil. Un ping ICMP viaja **dentro** de IP, dentro de Ethernet. Si capturas con **Wireshark**, expandes el árbol y ves exactamente qué campo está mal (checksum, TTL, MAC, etc.).

@section: TCP/IP: enlace a la realidad

* **Acceso a red (link):** Ethernet, Wi‑Fi, ARP.
* **Internet:** IPv4/IPv6, ICMP, routing.
* **Transporte:** TCP (fiabilidad, ventana), UDP (best effort).
* **Aplicación:** protocolos sobre sockets.

**Sockets** = IP + puerto + protocolo (TCP/UDP).

@section: Puertos y servicios

Los **puertos** son multiplexación en **capa de transporte** (TCP/UDP), no «capa 7» por sí solos. `443/tcp` es convención para HTTPS; el servidor escucha ahí salvo configuración distinta.

@section: Diagnóstico guiado por capa

1. **Capa 1–2:** link caído, errores FCS, STP bloqueando puertos.
2. **Capa 3:** sin ruta, subnet mal, firewall L3.
3. **Capa 4:** conexión TCP no establecida (SYN retransmitido), puerto cerrado.
4. **Capa 7:** HTTP 502, DNS NXDOMAIN, certificado TLS inválido.

No saltes a «es la aplicación» sin mirar si hay **conectividad IP** y **DNS**.

@section: Errores frecuentes

* Decir «capa 7» para cualquier cosa de usuario.
* Confundir **broadcast de capa 2** con **multicast IP**.
* Ignorar que **NAT** rompe end-to-end en capa 3/4.

@section: Laboratorio sugerido

1. En Linux, ejecuta `ss -tn` y `ip route` mientras abres un sitio HTTPS; relaciona socket con ruta por defecto.
2. Captura con `tcpdump` o Wireshark un handshake TCP y señala cabeceras IP/TCP.
3. Dibuja encapsulación ICMP dentro de IP dentro de Ethernet para un ping.

@quiz: ¿En qué capa del modelo OSI operan principalmente las direcciones MAC?
@option: Capa 3
@correct: Capa 2 (enlace de datos)
@option: Capa 7
