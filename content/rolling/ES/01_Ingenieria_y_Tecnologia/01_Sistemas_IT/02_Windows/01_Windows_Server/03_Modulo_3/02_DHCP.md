@title: DHCP: Ámbitos, Reservas y Failover
@icon: 🔢
@description: Asignando direcciones IP automáticamente en la red.
@order: 2

# DHCP: ámbitos, reservas y failover

**DHCP** asigna direcciones IPv4/IPv6, máscara, puerta de enlace, DNS y más **opciones**. En un instituto técnico debes dominar **ámbitos**, **exclusiones**, **reservas** y **failover** entre dos servidores para alta disponibilidad.

@section: Objetivos didácticos

*   Crear un **ámbito** IPv4 coherente con el plan de direccionamiento.
*   Definir **opciones 003 (router)**, **006 (DNS)**, **015 (DNS domain)**.
*   Configurar **DHCP failover** (hot-standby o load balancing).

@section: Componentes

*   **Ámbito:** rango de IPs a entregar (ej. `192.168.10.100–200`).
*   **Exclusión:** IPs dentro del rango que **no** debe entregar DHCP (servidores estáticos).
*   **Reserva:** MAC fija → IP fija (impresoras, APs, etc.).
*   **Superscope:** agrupa varios ámbitos en una misma red física (casos de migración).

@section: Opciones críticas

*   **003 Router:** gateway por defecto.
*   **006 DNS servers:** idealmente **DCs** o DNS internos, no el router del ISP.
*   **015 DNS domain:** sufijo para resolución de nombres cortos.

@section: Autorización en AD

En dominios, el servidor DHCP debe estar **autorizado** en AD para evitar servidores rogue en la red.

@section: Failover

**Dos modos** habituales:

*   **Load balance:** ambos servidores reparten el 50 % (o según ratio).
*   **Hot standby:** uno activo, otro en espera.

**Requisito:** reloj y red estables; **split scope** manual es legacy frente a failover integrado.

@section: IPv6

DHCPv6 o **SLAAC + RA** según diseño. Documenta si tu empresa usa **stateful** o **stateless** DHCPv6.

@section: Diagnóstico

*   `ipconfig /all` en cliente.
*   Consola DHCP → **Address Leases**, **Statistics**.
*   Eventos: **Microsoft-Windows-DHCP-Server/Operational**.

@section: Práctica

1.  Crea un ámbito de laboratorio y una reserva para una VM.
2.  Comprueba en el cliente **Lease Time** y **renovación** (`ipconfig /renew`).

@quiz: ¿Qué opción DHCP define la puerta de enlace predeterminada para los clientes?
@option: 006
@correct: 003 Router
@option: 015

@quiz: ¿Para qué sirve autorizar un servidor DHCP en Active Directory?
@option: Para que el servidor sea más rápido
@correct: Para impedir que servidores DHCP no autorizados entreguen en la red del dominio
@option: Para cifrar DHCP
