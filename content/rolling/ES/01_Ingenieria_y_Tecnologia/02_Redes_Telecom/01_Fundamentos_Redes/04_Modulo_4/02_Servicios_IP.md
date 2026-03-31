@title: Servicios IP: DHCP, DNS y NTP
@icon: 🌐
@description: Asignación dinámica, resolución de nombres y sincronización de tiempo.
@order: 2

# Servicios básicos: DHCP, DNS y NTP

Sin **DHCP**, los hosts no obtienen IP; sin **DNS**, las apps no resuelven nombres; sin **NTP**, los logs y certificados TLS se desordenan. Esta lección conecta **scopes**, **opciones** (opción 3 router, 6 DNS), **DNS recursivo/authoritative** y **stratum**.

@section: DHCP

**DORA** (Discover/Offer/Request/ACK). **Relay** (`ip helper-address`) reenvía broadcasts entre VLANs hacia servidor central.

**Reservas** por MAC para impresoras/servicios; **pools** separados por VLAN.

**Snooping** (lección L2) evita servidores rogue.

@section: DNS

* **Autoritativo** para tu zona `example.com`.
* **Recursivo** para clientes internos (resolver).

**Split-horizon DNS** sirve vistas distintas internas/externas. **DNSSEC** firma registros; validación en resolvers.

**TTL** afecta agilidad de cambios y carga de caches.

@section: NTP

**Stratum** indica distancia a reloj de referencia. **Authentication** entre servidores en entornos críticos.

**Leap seconds** y **smearing** en cloud providers: revisa documentación para sistemas distribuidos.

@section: Errores frecuentes

* DHCP sin **default gateway** en opciones.
* DNS interno sin **PTR** para troubleshooting de correo (SPF/DMARC colaterales).

@section: Laboratorio sugerido

1. Levanta `dnsmasq` o BIND en VM y crea zona interna `lab.local`.
2. Configura relay DHCP en router L3 hacia servidor central simulado.
3. Sincroniza hosts con `chrony`/`ntpd` y verifica `ntpstat`.

@quiz: ¿Para qué sirve principalmente un agente relay DHCP en un router L3?
@option: Cifrar DHCP
@correct: Reenviar solicitudes broadcast DHCP entre subredes hacia un servidor central
@option: Asignar VLANs automáticamente sin 802.1Q
