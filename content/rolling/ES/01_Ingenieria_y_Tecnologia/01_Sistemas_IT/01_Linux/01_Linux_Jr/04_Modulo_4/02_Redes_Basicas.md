@title: Red en Linux: interfaces, IP, DNS y diagnóstico básico
@icon: 📶
@description: Direcciones IP, interfaz, puerta de enlace y DNS; comandos ip, ping y rutas para localizar fallos habituales.
@order: 2

# Redes básicas en Linux: configuración y diagnóstico

Bienvenido a la clase de fontanería digital.

Hoy en día, un ordenador sin internet es básicamente una calculadora glorificada. La red lo es todo. Servidores, nubes, bases de datos, videojuegos... nada existe sin la red.

Pero, ¿cómo funciona realmente?
Cuando abres el navegador y escribes `google.com`, ocurre una secuencia de milagros tecnológicos en milisegundos. Si algo falla, el usuario normal dice "se ha caído el Wifi" y reinicia el router.
El usuario de Linux **no reinicia a ciegas**. El usuario de Linux interroga al sistema, encuentra el fallo y lo arregla con precisión quirúrgica.

En Windows, la configuración de red está escondida detrás de menús de configuración, paneles de control antiguos y asistentes automáticos.
En Linux, la red es **transparente**. Tienes control total sobre cada paquete de datos que entra y sale.

En esta guía masiva, vamos a diseccionar la pila de red. No vamos a ver teoría aburrida del Modelo OSI (eso es para el examen de Cisco). Vamos a ver **cómo sobrevivir en la trinchera**. Aprenderás a saber quién eres (IP), a gritar para ver si te escuchan (Ping), a usar la guía telefónica mundial (DNS) y a encontrar la puerta de salida (Gateway).

Prepárate. Vamos a conectar los cables.

@section: 1. Identidad: ¿Quién soy yo? (`ip addr`)

Lo primero que necesitas para estar en una red es una identidad. En el mundo real tienes un DNI y una dirección postal. En la red, tienes una **Dirección MAC** y una **Dirección IP**.

Antiguamente, el comando para ver esto era `ifconfig`. Si ves un tutorial que usa `ifconfig`, es un tutorial viejo (ese comando está "deprecated" o desaconsejado desde hace años).
El estándar moderno y profesional es el comando **`ip`**.

### 1.1 Listando Interfaces
Escribe en tu terminal:

```bash
$ ip addr
```
*(También vale `ip a` para los vagos).*

Verás una salida que puede asustar al principio. Vamos a descifrarla línea a línea. Normalmente verás dos bloques numerados:

**Bloque 1: La Interfaz Loopback (`lo`)**
```text
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
    inet 127.0.0.1/8 scope host lo
```
*   **¿Qué es?** Es una tarjeta de red virtual. Falsa.
*   **¿Para qué sirve?** Sirve para que el ordenador hable consigo mismo.
*   **La IP 127.0.0.1:** Esta dirección es sagrada. Significa **"Yo mismo"** o **"localhost"**. Si haces `ping 127.0.0.1` y no responde, tu sistema operativo está roto cerebralmente.
*   **Importancia:** Muchos servicios internos (como una base de datos) usan esta red para hablar con el servidor web que está en la misma máquina, sin salir a la red física.

**Bloque 2: La Interfaz Real (`eth0`, `enp3s0`, `wlan0`)**
```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    link/ether 00:1a:2b:3c:4d:5e brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.45/24 brd 192.168.1.255 scope global dynamic eth0
```
Aquí está la chicha.

1.  **El Nombre:**
    *   **`eth0`:** El nombre clásico para Ethernet (Cable).
    *   **`wlan0`:** El nombre clásico para WiFi.
    *   **`enp3s0` / `wlp2s0`:** Nombres modernos (Predictable Network Interface Names). Son feos, pero indican dónde está la tarjeta pinchada físicamente en la placa base. Acostúmbrate a ellos.

2.  **El Estado (`UP` vs `DOWN`):**
    Fíjate en los corchetes `<...>`. Si ves la palabra **`UP`**, la tarjeta está encendida. Si ves `DOWN`, está apagada.
    *   *¿Cómo la enciendo?* `sudo ip link set eth0 up`.

3.  **La Dirección Física (`link/ether`):**
    `00:1a:2b:3c:4d:5e`
    Esta es la **MAC Address**. Es el número de serie grabado a fuego en el chip de la tarjeta por el fabricante. Es única en el mundo. Es tu huella dactilar hardware.

4.  **La Dirección Lógica (`inet`):**
    `192.168.1.45/24`
    Esta es tu **Dirección IP (IPv4)**. Es tu dirección postal actual. Te la ha asignado el Router (vía DHCP) o la has puesto tú manualmente.
    *   Si no ves una línea que empiece por `inet`, **no tienes dirección IP**. Estás desconectado del mundo lógico, aunque el cable esté enchufado.

### 1.2 Entendiendo la IP y la Máscara (/24)
Verás ese `/24` al final de la IP. Es la notación CIDR (Classless Inter-Domain Routing).
Simplificando muchísimo para novatos:
*   Una IP tiene 4 números: `A.B.C.D`.
*   `/24` significa que los primeros 3 números (`A.B.C`) son el **Nombre de la Calle** (La Red) y el último (`D`) es el **Número de tu Casa**.
*   Si tu IP es `192.168.1.45/24`:
    *   Vives en la calle `192.168.1`.
    *   Eres el vecino número `45`.
    *   Solo puedes hablar directamente con los vecinos `1` al `254` de esa misma calle. Para hablar con alguien de la calle `192.168.50`, necesitas ir al Router.

@section: 2. Sonar: Comprobando Vida (`ping`)

Una vez que sabes quién eres (`ip addr`), quieres saber si hay alguien más ahí fuera.
Usamos el comando **`ping`**.

`ping` envía un pequeño paquete de datos (ICMP Echo Request) a una dirección y espera que esa máquina le devuelva el paquete (ICMP Echo Reply). Es el sonar de los submarinos.

### 2.1 El Ping Básico
```bash
$ ping 8.8.8.8
```
*(8.8.8.8 es el servidor DNS de Google, famoso por estar siempre encendido. Es el estándar mundial para comprobar si tienes internet).*

Verás algo así:
`64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=14.5 ms`
`64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=14.2 ms`

**Cómo leerlo:**
1.  **icmp_seq:** Número de secuencia. Si salta del 1 al 3, has perdido el paquete 2 (Packet Loss). Tu conexión es inestable.
2.  **time:** Latencia. Cuánto tiempo ha tardado la señal en ir a California y volver.
    *   `< 10ms`: Estás en la misma ciudad o tienes fibra óptica divina.
    *   `20-50ms`: Normal.
    *   `> 200ms`: La conexión es lenta (o estás usando satélite).
    *   `Request timeout`: Nadie responde. O el cable está roto, o hay un Firewall bloqueando el saludo.

**¡Diferencia vital con Windows!**
*   En Windows, `ping` se para solo después de 4 intentos.
*   En Linux, `ping` es **infinito**. Seguirá hasta el fin de los tiempos o hasta que tú lo pares.
*   **PARA PARARLO:** Pulsa `Ctrl + C`.

### 2.2 Variaciones de Ping
*   **Solo 3 intentos:** `ping -c 3 google.com` (Count).
*   **Solo ver si está vivo (sin spam):** Puedes usar scripts, pero visualmente `ping` es lo mejor.

@quiz: ¿Qué significa si ejecutas `ping 8.8.8.8` y obtienes el mensaje "Network is unreachable"?
@option: Que Google se ha caído.
@correct: Que tu ordenador no tiene una ruta para salir a internet (probablemente no tienes IP o Gateway configurado).
@option: Que el cable está desconectado físicamente.
@option: Que el servidor DNS ha fallado.

@section: 3. La Guía Telefónica: DNS (`nslookup`, `dig`)

Los ordenadores aman los números (`142.250.200.14`).
Los humanos aman los nombres (`google.com`).

El **DNS (Domain Name System)** es la infraestructura que traduce Nombres a Números. Es la guía telefónica de Internet.
Sin DNS, tendrías que memorizar las IPs de todas tus webs favoritas.

### 3.1 Probando el DNS
A veces tienes internet (el ping a `8.8.8.8` funciona), pero no puedes navegar. El navegador dice "No se puede resolver la dirección".
Eso es un fallo de DNS.

Para diagnosticarlo, preguntamos al sistema: "¿Quién es google.com?".

**Herramienta Básica: `nslookup`**
```bash
$ nslookup google.com
Server:     127.0.0.53
Address:    127.0.0.53#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.200.14
```
Si te responde con una Address, el DNS funciona.
Si dice "Servfail" o "NXDOMAIN" o se queda pensando 10 segundos, tu DNS está roto.

**Herramienta Pro: `dig`**
Los administradores prefieren `dig` (Domain Information Groper) porque da más detalles técnicos sin filtrar.
```bash
$ dig google.com
```
Busca la sección "ANSWER SECTION". Si está vacía, malo.

### 3.2 ¿Quién es mi servidor DNS?
¿A quién le está preguntando tu ordenador?
Esa configuración vive en un archivo sagrado: **`/etc/resolv.conf`**.

Haz un `cat /etc/resolv.conf`.
Verás una línea `nameserver X.X.X.X`.
*   En sistemas modernos con `systemd-resolved`, suele ser `127.0.0.53` (un proxy local).
*   En servidores clásicos, verás `1.1.1.1` (Cloudflare) o `8.8.8.8` (Google).

### 3.3 El Archivo `/etc/hosts` (La libreta de notas)
Antes de preguntar al DNS mundial, tu Linux mira en su bolsillo, en una libreta pequeña llamada `/etc/hosts`.
Tú puedes manipular la realidad aquí.

Si editas este archivo (como root) y añades:
`127.0.0.1   facebook.com`

A partir de ese momento, si intentas entrar en Facebook, tu ordenador se conectará a sí mismo (localhost) y fallará. Es una forma primitiva pero efectiva de bloquear webs o de probar desarrollos locales (hacer que `miweb.test` apunte a tu servidor de pruebas).

@section: 4. El Mapa: Routing y Gateway (`ip route`)

Tienes IP. El DNS funciona. Pero no puedes salir a internet.
Es posible que tu ordenador no sepa **dónde está la puerta de salida**.

En una red local (tu casa), tu ordenador solo sabe hablar con los vecinos de su misma calle (subred). Si quieres enviar una carta a China (Internet), tienes que dársela al Cartero que sabe salir del barrio.
Ese cartero es el **Router** (o Gateway / Puerta de Enlace).

### 4.1 Consultando la Tabla de Rutas
Escribe:
```bash
$ ip route
```
Verás algo así:
`default via 192.168.1.1 dev eth0 proto dhcp ...`
`192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.45`

La línea vital es la primera: **`default via ...`**.
*   **Default:** Significa "Para todo lo que no sepa dónde está (todo Internet)".
*   **Via 192.168.1.1:** "Envíalo a esta dirección IP".

Esa IP (`192.168.1.1`) es tu Router.
Si esa línea no existe, tu ordenador está atrapado en la red local. No tiene puerta de salida.

**Prueba de Fuego:**
Intenta hacer ping a tu Gateway.
`ping 192.168.1.1`
*   Si funciona: Tu conexión con el router está bien. El problema es de ahí para afuera (culpa de la compañía telefónica).
*   Si falla: El problema está entre tu PC y el router (WiFi malo, cable roto, router apagado).

@section: 5. Puertos y Sockets: Las Habitaciones del Hotel

Tener una IP hace que el paquete llegue a tu ordenador. Pero, ¿a qué aplicación se lo entregamos?
¿Es un email? ¿Es una web? ¿Es una partida de Minecraft?

Para eso sirven los **Puertos**.
Imagina que la IP es la dirección del Edificio (Hotel). Los Puertos son los números de las habitaciones.
*   Habitación 80: Servidor Web (HTTP).
*   Habitación 443: Servidor Web Seguro (HTTPS).
*   Habitación 22: Servidor SSH.

### ¿Quién está escuchando? (`ss` / `netstat`)
A veces intentas conectar y te rechazan ("Connection Refused"). Puede que el programa no esté corriendo o esté escuchando en el puerto equivocado.

El comando moderno es **`ss`** (Socket Stat), aunque los viejos del lugar usan `netstat`.

```bash
# Ver puertos TCP (t) que están escuchando (l) y mostrar números (n)
$ ss -tln
```
Salida:
`LISTEN   0   128   0.0.0.0:22    0.0.0.0:*`
Esto significa: "Hay alguien (SSH) escuchando en el puerto 22 de todas las IPs (0.0.0.0), esperando conexiones".

Si arrancas un servidor web y no lo ves aquí en el puerto 80, es que no ha arrancado bien.

@section: 6. Protocolo de Emergencias: Flujo de Resolución de Problemas

Tu jefe grita: "¡El servidor no tiene red!". No entres en pánico. Sigue este algoritmo sagrado paso a paso. Se llama **Modelo OSI de abajo a arriba**.

**Paso 1: Capa Física y Enlace (El Cable)**
*   ¿Tiene luces la tarjeta de red?
*   Comando: `ip link`.
*   ¿Dice `state UP` o `state DOWN`?
*   *Solución:* Si es DOWN, revisa el cable o haz `sudo ip link set eth0 up`.

**Paso 2: Capa de Red (La IP)**
*   ¿Tengo identidad?
*   Comando: `ip addr`.
*   ¿Tengo una IP (inet 192...)?
*   *Solución:* Si no, pide una al router (DHCP) con `sudo dhclient -v`.

**Paso 3: Capa de Red Local (El Vecindario)**
*   ¿Puedo ver a mi Router?
*   Comando: `ip route` (para ver quién es el router) -> `ping 192.168.1.1`.
*   *Solución:* Si falla, el router está apagado o el firewall te bloquea.

**Paso 4: Capa de Internet (El Mundo Exterior)**
*   ¿Puedo salir fuera?
*   Comando: `ping 8.8.8.8` (Ping a una IP, no a un nombre).
*   *Solución:* Si falla pero el paso 3 funcionaba, tu proveedor de internet ha cortado la línea o el router no está enrutando.

**Paso 5: Capa de Resolución de Nombres (DNS)**
*   ¿Puedo traducir nombres?
*   Comando: `ping google.com` o `nslookup google.com`.
*   *Solución:* Si el paso 4 funcionaba (ping 8.8.8.8 OK) pero este falla, **ES SIEMPRE EL DNS**. Edita `/etc/resolv.conf` y pon `nameserver 8.8.8.8`.

**Paso 6: Capa de Aplicación (El Servicio)**
*   Todo lo anterior va bien, pero mi web no carga.
*   Comando: `curl -I google.com` o `ss -tln`.
*   *Solución:* El servidor web se ha caído o el firewall del servidor (iptables/ufw) está rechazando el puerto 80.

@section: 7. Resumen / Cheat Sheet

| Comando | Acción | Analogía |
| :--- | :--- | :--- |
| `ip addr` | Ver IPs e interfaces | Mirar mi DNI |
| `ip link set eth0 up` | Encender tarjeta | Despertar al portero |
| `ping 8.8.8.8` | Comprobar conectividad IP | Usar el sonar |
| `ip route` | Ver tabla de rutas | Mirar el mapa de salida |
| `nslookup google.com` | Consultar DNS | Mirar la guía telefónica |
| `dig google.com` | Consultar DNS (Pro) | Interrogar a la operadora |
| `ss -tln` | Ver puertos escuchando | Ver qué habitaciones del hotel están abiertas |
| `/etc/resolv.conf` | Configuración DNS | La lista de guías telefónicas |
| `/etc/hosts` | Sobrescribir DNS local | La libreta de notas privada |

¡Felicidades! Ahora sabes más de redes que la mayoría de la gente. La próxima vez que "se caiga el wifi", no reiniciarás el router a ciegas. Sabrás exactamente si es el DNS, el Gateway o la capa física. Eres el dueño de tus paquetes.