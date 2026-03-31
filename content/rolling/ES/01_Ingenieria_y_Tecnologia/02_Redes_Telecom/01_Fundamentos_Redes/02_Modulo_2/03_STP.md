@title: STP/RSTP: evitar tormentas de broadcast
@icon: 🌳
@description: Raíz, costes de puerto, estados y protecciones (BPDU Guard, Root Guard).
@order: 3

# Spanning Tree: cuando la redundancia sin control mata la red

**STP** (802.1D) y **RSTP** (802.1w) previenen **loops** de capa 2 bloqueando puertos redundantes. Sin STP, **broadcasts** se multiplican hasta colapsar la LAN. Esta lección explica **raíz**, **costes**, **estados** y **protecciones** (BPDU Guard, Root Guard).

@section: Idea raíz

Se elige un **root bridge** (bridge ID). Cada switch calcula **root port** (mejor camino al root) y **designated ports**; otros puertos quedan **blocking/discarding**.

**Costes** dependen de velocidad; rutas más rápidas suelen preferirse.

@section: RSTP vs STP clásico

**RSTP** converge más rápido (segundos vs decenas de segundos). **MST** (802.1s) agrupa VLANs en instancias para cargar balancear mejor.

@section: Protecciones

* **BPDU Guard:** deshabilita puerto de acceso si recibe BPDU (posible loop).
* **Root Guard:** impide que un puerto designado sea root inesperado.
* **Loop Guard:** protege ante enlaces unidireccionales.

@section: Portfast

**Edge port** para conexión a host final: salta estados de escucha/aprendizaje. **Nunca** en enlaces a otros switches.

@section: Errores frecuentes

* Conectar dos switches con dos cables **sin** STP/LAG → loop.
* **Timers** mal ajustados en redes grandes (menos común con RSTP).

@section: Laboratorio sugerido

1. Conecta dos switches con dos cables y observa bloqueo STP.
2. Activa EtherChannel y compara convergencia.
3. Habilita BPDU Guard en un puerto de acceso y conecta un switch pequeño → verifica err-disable.

@quiz: ¿Qué condición intenta prevenir STP en una LAN conmutada?
@option: Falta de IPv6
@correct: Bucles de capa 2 que provocan tormentas de broadcast
@option: Pérdida de paquetes TCP
