@title: Diseño de Sitios y Replicación AD
@icon: 🔁
@description: Optimizando la replicación de Active Directory en redes distribuidas.
@order: 5

# Diseño de sitios y replicación AD

Los **sitios** en Active Directory modelan la **topología de red física**: una LAN rápida = un sitio; una WAN lenta = otro sitio. El **KCC** (Knowledge Consistency Checker) crea automáticamente la topología de replicación, pero **tú** defines **subredes**, **costes** y **puentes de sitio** para que el tráfico de directorio sea eficiente.

@section: Objetivos didácticos

*   Crear **sitios**, **subredes** y **enlaces de sitio**.
*   Explicar **replicación intra-sitio** vs **inter-sitio**.
*   Usar **RODC** y **GC** en oficinas remotas.

@section: ¿Por qué sitios?

Sin sitios bien definidos, la replicación puede:

*   Saturar enlaces WAN lentos.
*   Hacer que los usuarios autentiquen contra DC **lejanos** en lugar de los cercanos.

**Regla:** cada subred IPv4/IPv6 debe estar **asociada** a un sitio.

@section: Objetos clave

*   **Site:** contenedor lógico (ej. `Sede-Madrid`, `Sede-Lima`).
*   **Subnet:** asociada a un sitio.
*   **Site link:** conecta sitios; tiene **coste** y **intervalo** de replicación.
*   **Bridgehead servers:** DC que centralizan replicación inter-sitio (elegidos automáticamente).

@section: Puentes de sitio (Site link bridges)

Por defecto, todos los enlaces en un **site link bridge** permiten transitividad. En redes IP **no totalmente enrutadas**, puede hacer falta **desactivar** el bridging o ajustar manualmente (casos avanzados).

@section: Colocación de GC y FSMO

*   **GC** en sitios con usuarios multidominio.
*   **Infrastructure Master** no debe estar en un DC que sea también GC en multidominio (regla clásica; revisar documentación de tu versión y topología).

@section: Herramientas

*   **Active Directory Sites and Services** (`dssite.msc`).
*   `repadmin /showrepl`, `repadmin /syncall`.

@section: Laboratorio

1.  Crea dos sitios y asocia subredes distintas.
2.  Mueve un DC a un sitio remoto simulado.
3.  Observa en **Event Viewer** la replicación de **Directory Service**.

@quiz: ¿Qué objeto de topología define el coste e intervalo entre sedes conectadas por WAN?
@option: OU
@correct: Site link (enlace de sitio)
@option: GPO

@quiz: ¿Qué componente de AD genera automáticamente la topología de replicación entre controladores de dominio?
@option: DHCP
@correct: KCC (Knowledge Consistency Checker)
@option: DNS scavenging
