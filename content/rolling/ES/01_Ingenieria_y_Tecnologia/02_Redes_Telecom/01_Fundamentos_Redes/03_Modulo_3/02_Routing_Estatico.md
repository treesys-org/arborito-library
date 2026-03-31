@title: Routing estático: simplicidad y riesgo operativo
@icon: 📍
@description: Rutas estáticas, recursivas, flotantes y troubleshooting.
@order: 2

# Routing estático: control total, mantenimiento manual

Las **rutas estáticas** son ideales para **stub networks**, **default** en hosts, o **agregados** simples. Pero **no escalan** con cambios frecuentes y pueden generar **black holes** si no hay **reachability** al next-hop. Esta lección cubre **next-hop**, **interfaz saliente**, **flotantes** y verificación.

@section: Next-hop vs salida

**Next-hop IP:** el router resuelve L2 hacia esa IP. **Salida** directa en enlaces punto a punto puede ser válida pero menos explícita en multiacceso.

**Proxy ARP** puede enmascarar errores; mejor rutas explícitas.

@section: Rutas flotantes

Misma red destino con **AD mayor** actúa como backup si la principal desaparece.

**Track** IP SLA (Cisco) o **BFD** en otros entornos para retirar ruta activa.

@section: Hosts

Los endpoints usan **default gateway** estático o DHCP. Rutas estáticas en servidores multi-homed requieren cuidado con **asimetría**.

@section: Troubleshooting

* `ping` al next-hop.
* `show ip route` / `ip route` en Linux.
* **Recursive lookup** falla si no hay ruta al next-hop.

@section: Errores frecuentes

* Estática hacia red remota sin ruta de retorno simétrica.
* **Overlapping** con dinámicos sin política clara.

@section: Laboratorio sugerido

1. Configura dos routers con rutas estáticas recíprocas y valida ping end-to-end.
2. Introduce ruta flotante y simula caída del enlace primario.
3. Documenta matriz de rutas para un sitio ficticio.

@quiz: ¿Cuándo suele preferirse una ruta estática flotante?
@option: Para reemplazar OSPF siempre
@correct: Como respaldo cuando la ruta principal con menor AD desaparece
@option: Para aumentar la métrica de BGP
