@title: EtherChannel / LAG: agregación de enlaces
@icon: 🔗
@description: LACP vs PAgP, balanceo de carga y capa 2 vs capa 3.
@order: 4

# Agregación de enlaces: más ancho de banda y redundancia

**EtherChannel** (Cisco) o **LAG** (802.3ad **LACP**) agrupa varios enlaces físicos en un **único canal lógico** para **STP** evita bloquear todos menos uno. También permite **balanceo de carga** por flujo (hash). Esta lección compara **LACP** vs **PAgP**, **L2 vs L3**, y **errores**.

@section: LACP vs estático

**LACP** negocia miembros activos/standby; **modo on** estático sin negociación (riesgo si el otro extremo no coincide).

**PAgP** es propietario Cisco; preferible **LACP** multi-vendor.

@section: Balanceo

El hash suele usar **src/dst MAC**, **IP**, **puertos** según configuración. Un solo flujo **no** supera la velocidad de un miembro; muchos flujos sí reparten carga.

@section: L3 EtherChannel

Interfaces **routed port** con port-channel L3: vecinos OSPF/BGP sobre canal agregado.

@section: Errores frecuentes

* **Mismatched** velocidad/duplex en miembros.
* Mezclar VLANs permitidas en trunk miembro del canal.

@section: Laboratorio sugerido

1. Crea un Port-channel entre dos switches con LACP activo/pasivo.
2. Prueba `show etherchannel summary` y verifica flags.
3. Genera múltiples flujos iperf y observa distribución de interfaces.

@quiz: ¿Qué estándar abierto suele usarse para negociar agregación de enlaces entre switches?
@option: PAgP
@correct: LACP (802.3ad)
@option: DTP
