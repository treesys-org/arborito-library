@title: NAT y PAT: traducción de direcciones en el borde
@icon: 🔄
@description: SNAT/DNAT, PAT sobrecarga, NAT hairpin y implicaciones para apps.
@order: 5

# NAT/PAT: compartir IPv4 pública sin magia

**NAT** traduce direcciones **privadas↔públicas**. **PAT** (NAPT) multiplexa muchos hosts privados en **una IP pública** usando **puertos**. Esencial para IPv4; rompe **end-to-end** y complica **VoIP**, **IPsec**, **FTP** si no hay **ALG** o **helpers**. Esta lección cubre **SNAT/DNAT**, **hairpin**, **NAT444** y troubleshooting.

@section: SNAT vs DNAT

* **SNAT (masquerade):** salida a Internet con IP pública del router.
* **DNAT (port forward):** servicio interno publicado `203.0.113.5:443 → 10.0.0.10:443`.

**Stateful** NAT mantiene tablas de sesión.

@section: PAT

Miles de flujos comparten IP pública; clave `(src IP, src port)` única en tabla.

**Agotamiento de puertos** en CGNAT masivo → problemas para apps que abren muchas conexiones.

@section: Hairpin NAT

Cliente interno accede a **VIP pública** que DNAT a servidor interno: requiere **NAT loopback/hairpin** en el router/firewall.

@section: NAT y seguridad

NAT **no es firewall** por sí solo; es **obfuscación**. Usa **stateful firewall** explícito.

@section: Errores frecuentes

* **Asymmetric routing** con múltiples ISPs y NAT inconsistente.
* **ALG** deshabilitado rompe FTP/SIP en algunos casos.

@section: Laboratorio sugerido

1. Configura SNAT en un router Linux (`iptables`/`nft`) o firewall de lab.
2. Publica un servidor web interno con DNAT y prueba desde fuera.
3. Prueba hairpin desde la LAN si el equipo lo soporta.

@quiz: ¿Qué es principalmente el PAT (NAPT)?
@option: Traducción de nombres DNS
@correct: NAT con sobrecarga de puertos para multiplexar muchas sesiones sobre una IP pública
@option: Cifrado TLS
