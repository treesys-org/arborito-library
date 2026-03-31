@title: ACLs: filtrado en routers y firewalls
@icon: 🧱
@description: Estándar vs extendidas, orden de evaluación, objetos y ACL reflexivas.
@order: 1

# Listas de acceso: política explícita en capa 3/4

Las **ACL** filtran tráfico por **origen/destino IP**, **protocolo**, **puertos**, a veces **flags TCP**. En routers Cisco hay **estándar (1-99)** y **extendidas**; los firewalls modernos usan **objetos** y **reglas ordenadas**. Esta lección cubre **orden**, **implicit deny**, **logging** y **reflexivas**.

@section: Orden de evaluación

Las reglas se evalúan **de arriba abajo**; primera coincidencia gana. **Implicit deny** al final niega lo no permitido.

**Contadores** ayudan a ver reglas muertas o mal usadas.

@section: ACL extendida típica

Permitir `10.0.0.0/24` a `tcp/443` hacia `198.51.100.0/24`, denegar resto. **Established** en TCP para respuestas relacionadas (cuidado con estado vs stateless).

**Object-groups** reducen repetición.

@section: ACL reflexivas (reflexive)

En entornos stateless router-only, **reflexive** permite tráfico de retorno asociado a sesiones iniciadas internamente.

Los **firewalls stateful** reemplazan esto con tablas de conexión.

@section: IPv6 ACL

Sintaxis paralela; no olvides **ICMPv6** necesario para NDP (permite reglas específicas).

@section: Errores frecuentes

* Permitir demasiado ancho (`any any`) «temporal» que nunca se cierra.
* Olvidar **path de retorno** en routing asimétrico.

@section: Laboratorio sugerido

1. Crea ACL extendida en un router de laboratorio y verifica con `show access-lists`.
2. Genera tráfico permitido y denegado; observa hits.
3. Traduce la misma política a sintaxis `nftables` en Linux para comparar conceptos.

@quiz: ¿Qué ocurre si ninguna línea de una ACL coincide con un paquete IPv4?
@option: Se permite por defecto
@correct: Se deniega implícitamente (implicit deny)
@option: Se reenvía al CPU del switch
