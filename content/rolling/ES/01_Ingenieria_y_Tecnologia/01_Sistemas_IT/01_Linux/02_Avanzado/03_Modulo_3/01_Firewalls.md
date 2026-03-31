@title: Cortafuegos en Linux: Netfilter, nftables e iptables
@icon: 🔥
@description: Flujo de paquetes en el kernel, tablas y cadenas, reglas básicas, NAT y transición de iptables a nftables (y frontends como ufw/firewalld).
@order: 1

# Firewalls en Linux: Netfilter, iptables y nftables

Aquí verás el **flujo de paquetes** hasta **Netfilter**, el modelo de **tablas y cadenas**, reglas con **nftables** e **iptables**, **NAT** básico y el papel de **firewalld** / **ufw** como capas de alto nivel en RHEL y Debian.

@section: Mapa LPIC-2 — Módulo 3 (seguridad del sistema y red)

Cobertura tipo **LPIC-2 (202 / 206)** + práctica **RHEL**:

*   **202 — Seguridad del sistema:** firewalls, restricción de servicios, auditoría, TLS, autenticación centralizada.
*   **206 — Firewall y NAT:** Netfilter, `nftables`/`iptables`, zonas `firewalld`, reglas persistentes.
*   **SELinux/AppArmor:** endurecimiento de acceso obligatorio (MAC) frente a solo DAC.
*   **RHEL:** `firewall-cmd --permanent`, SELinux; **Debian:** `nft`/`iptables`, `ufw` como capa simplificada.

En el mundo de la ciberseguridad, un servidor sin firewall es como una casa con las puertas abiertas en un barrio peligroso. Tardará minutos en ser invadido por bots.

En Linux, el firewall no es un programa que instalas; es una parte intrínseca del propio **Kernel**. Se llama el subsistema **Netfilter**.
Las herramientas que usamos (`iptables`, `nftables`, `ufw`, `firewalld`) son solo "mandos a distancia" para hablar con el Kernel y decirle qué hacer con los paquetes de red.

En esta lección vamos a bajar al nivel del paquete. Vamos a entender cómo fluye un bit desde que entra por la tarjeta de red hasta que llega a tu aplicación, y cómo interceptarlo para permitirlo, bloquearlo o modificarlo (NAT).

@section: 1. El Flujo del Paquete: Hooks de Netfilter

Para escribir reglas efectivas, debes visualizar el camino del paquete. Netfilter tiene 5 puntos de control (Hooks) donde podemos detener el tráfico.

1.  **PREROUTING:** El paquete acaba de llegar a la tarjeta de red. Aún no sabemos si es para nosotros o si solo estamos de paso (router). Aquí es donde se hace **DNAT** (Destination NAT / Port Forwarding).
2.  **INPUT:** El paquete ha sido examinado y el Kernel ha decidido que **es para nosotros** (para un proceso local). Aquí es donde bloqueamos ataques entrantes a nuestro servidor.
3.  **FORWARD:** El paquete no es para nosotros, pero tenemos activado el reenvío (`ip_forward=1`). Somos un router y el paquete va a otro sitio. Aquí filtramos el tráfico que pasa *a través* de nosotros.
4.  **OUTPUT:** Un proceso local (ej: tu navegador o un virus) quiere enviar un paquete fuera. Aquí filtramos lo que sale.
5.  **POSTROUTING:** El paquete ya se va por la tarjeta de red. Aquí es donde se hace **SNAT** (Source NAT / Masquerade), típico en routers para dar internet a una LAN.

@section: 2. iptables: El Veterano

`iptables` ha sido el estándar durante décadas. Aunque está siendo reemplazado por `nftables`, sigue estando en el 90% de los servidores y tutoriales. Debes dominarlo.

### Tablas, Cadenas y Reglas
La estructura es jerárquica:
*   **Tablas:** Agrupan funciones.
    *   `filter`: La tabla por defecto. Para decidir quién pasa y quién no.
    *   `nat`: Para modificar direcciones (Port Forwarding, compartir internet).
    *   `mangle`: Para modificar paquetes (TOS, TTL).
*   **Cadenas (Chains):** Corresponden a los Hooks (INPUT, OUTPUT, FORWARD...).
*   **Reglas:** Las instrucciones ("Si viene de la IP X, Bloquea").

### Sintaxis Básica
`iptables -t [tabla] -[A/I/D] [cadena] [condiciones] -j [ACCIÓN]`

*   `-t filter`: Tabla filtro (por defecto, se puede omitir).
*   `-A`: Append (Añadir al final).
*   `-I`: Insert (Insertar al principio, prioridad).
*   `-D`: Delete (Borrar).
*   `-j`: Jump (Saltar a la acción: ACCEPT, DROP, REJECT, LOG).

### Escenario 1: El Firewall Personal (Bloquear todo, permitir SSH y Web)
Esta es la configuración base para cualquier servidor.

1.  **Ver lo que hay:**
    `sudo iptables -L -n -v` (Listar, numérico, verboso).

2.  **Establecer Política por Defecto:**
    Si ninguna regla coincide, ¿qué hacemos? Por seguridad: **CERRAR TODO**.
    ```bash
    sudo iptables -P INPUT DROP
    sudo iptables -P FORWARD DROP
    sudo iptables -P OUTPUT ACCEPT
    ```
    *¡CUIDADO! Si haces esto por SSH sin haber permitido SSH antes, te desconectarás a ti mismo y perderás el servidor. Siempre permite SSH antes de cambiar la política a DROP.*

3.  **Permitir tráfico local (Loopback):**
    Vital. Muchas apps hablan consigo mismas en 127.0.0.1.
    ```bash
    sudo iptables -A INPUT -i lo -j ACCEPT
    ```

4.  **Permitir conexiones establecidas (Stateful Firewall):**
    Si yo pido una web a Google (OUTPUT), la respuesta de Google (INPUT) debe entrar. No quiero bloquear las respuestas a mis propias preguntas.
    ```bash
    sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
    ```
    *Nota:* `conntrack` es el módulo que recuerda las conexiones. Es la magia que hace que el firewall sea "inteligente".

5.  **Permitir SSH (Puerto 22):**
    ```bash
    sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    ```

6.  **Permitir Web (80 y 443):**
    ```bash
    sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
    ```

### Escenario 2: Bloquear un atacante
Ves una IP china (`1.2.3.4`) intentando hackearte en los logs.
```bash
sudo iptables -I INPUT -s 1.2.3.4 -j DROP
```
Usamos `-I` (Insertar) para ponerla la primera. Si la pusiéramos al final (`-A`) y tuviéramos una regla previa que permite todo el tráfico web, el atacante pasaría. **El orden importa: la primera regla que coincide gana.**

### Persistencia
Las reglas de iptables están en memoria RAM. Si reinicias, se borran.
Para guardarlas:
*   Debian/Ubuntu: `sudo apt install iptables-persistent`. Durante la instalación te preguntará si quieres guardar las reglas actuales.
*   Manual: `iptables-save > /etc/iptables/rules.v4`.

@quiz: Has configurado la política por defecto de INPUT a DROP. Has permitido el puerto 80. Intentas navegar desde el servidor hacia google.com pero no funciona (DNS falla o no carga). ¿Qué regla crítica has olvidado?
@option: Permitir el puerto 443.
@option: Permitir tráfico UDP.
@correct: Permitir tráfico ESTABLISHED y RELATED. El servidor envía la petición (OUTPUT permitido), pero el firewall bloquea la respuesta de Google (INPUT) porque no sabe que es una respuesta a algo que tú pediste.
@option: Reiniciar el router.

@section: 3. UFW: Firewall para Seres Humanos

`iptables` es difícil. La sintaxis es larga y propensa a errores.
**UFW (Uncomplicated Firewall)** es una capa de abstracción sobre iptables estándar en Ubuntu. No es un firewall distinto, es una forma fácil de configurar iptables.

### Comandos Esenciales
*   **Estado:** `sudo ufw status verbose`.
*   **Activar:** `sudo ufw enable`.
*   **Resetear:** `sudo ufw reset` (Borra todo y deshabilita).

### Configuración Típica
```bash
# 1. Denegar todo lo entrante por defecto
sudo ufw default deny incoming

# 2. Permitir todo lo saliente
sudo ufw default allow outgoing

# 3. Permitir SSH (Busca el puerto estándar 22 automáticamente)
sudo ufw allow ssh

# 4. Permitir Web
sudo ufw allow http
sudo ufw allow https

# 5. Permitir un rango específico (ej: red de la oficina) al puerto MySQL
sudo ufw allow from 192.168.1.0/24 to any port 3306

# 6. Activar
sudo ufw enable
```

### Borrar reglas
UFW numera las reglas.
`sudo ufw status numbered`
Para borrar la regla 3:
`sudo ufw delete 3`

**Limitación de Rate (Anti-Bruteforce):**
UFW tiene una función genial preintegrada.
`sudo ufw limit ssh`
Esto permite SSH, pero si alguien intenta conectar 6 veces en 30 segundos, bloquea esa IP temporalmente. Es un mini Fail2Ban integrado.

@section: 4. nftables: El Futuro

`iptables` tiene problemas de rendimiento con miles de reglas y su código es complejo.
**nftables** es el sucesor moderno. Viene por defecto en Debian 10+, Fedora, RHEL 8+.
Muchas veces, cuando usas comandos `iptables` hoy en día, en realidad estás usando un traductor que convierte tus comandos a bytecode de `nftables` en el kernel.

### Diferencias Clave
*   **Sintaxis unificada:** Una sola herramienta `nft` para IPv4, IPv6, ARP y Bridge (antes tenías `iptables`, `ip6tables`, `arptables`...).
*   **Rendimiento:** Usa una máquina virtual en el kernel para procesar paquetes mucho más rápido.
*   **Conjuntos (Sets):** Puedes definir un conjunto de IPs `{ 1.1.1.1, 2.2.2.2, ... }` y crear una sola regla que diga "Si la IP está en este conjunto, DROP". En iptables tendrías que crear una regla por cada IP (lento y secuencial).

### Ejemplo Básico nftables
Archivo `/etc/nftables.conf`:

```nft
flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;

        # Permitir local y establecidas
        iifname "lo" accept
        ct state established,related accept

        # Permitir SSH y Web
        tcp dport { 22, 80, 443 } accept
        
        # Permitir ICMP (Ping)
        ip protocol icmp accept
    }
    chain forward {
        type filter hook forward priority 0; policy drop;
    }
    chain output {
        type filter hook output priority 0; policy accept;
    }
}
```
Fíjate en `tcp dport { 22, 80, 443 }`. En iptables serían 3 reglas. Aquí es una sola operación de búsqueda en un set. Mucho más eficiente.

Para aplicar: `sudo nft -f /etc/nftables.conf`.

@section: 5. NAT y Port Forwarding (El Servidor como Router)

A veces tu servidor Linux actúa como gateway para una red privada, o quieres que una petición al puerto 8080 se reenvíe a una IP interna. Eso es **NAT**.

### Habilitar Forwarding
Primero, el Kernel debe permitir pasar paquetes de una interfaz a otra.
Editar `/etc/sysctl.conf`:
`net.ipv4.ip_forward=1`
Aplicar: `sudo sysctl -p`.

### Masquerade (SNAT) - Compartir Internet
Tienes dos tarjetas: `eth0` (Internet) y `eth1` (LAN interna 10.0.0.0/24). Quieres que la LAN tenga internet.
```bash
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
sudo iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
```
Esto dice: "Todo lo que salga por eth0, cámbiale la IP origen para que parezca que sale de mí (el router)".

### Port Forwarding (DNAT)
Quieres que si alguien conecta a tu IP pública en el puerto 8080, el tráfico se mande a un servidor interno (10.0.0.5) en el puerto 80.
```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination 10.0.0.5:80
sudo iptables -A FORWARD -p tcp -d 10.0.0.5 --dport 80 -j ACCEPT
```

@section: 6. Logs y Auditoría

¿Cómo sabes si tu firewall está bloqueando algo que no debe?
Tienes que activar el log.

En **UFW**:
`sudo ufw logging on`
Los logs van a `/var/log/ufw.log`.

En **iptables** manual:
Añade una regla LOG justo antes del DROP final.
```bash
sudo iptables -A INPUT -j LOG --log-prefix "IPTables-Dropped: "
```
Esto escribirá en `/var/log/syslog` (o `kern.log`) cada paquete que llegue a ese punto, con el prefijo indicado, para que puedas depurar qué está pasando.

@quiz: ¿Cuál es la ventaja principal de usar Sets (conjuntos) en nftables frente a las reglas secuenciales de iptables?
@option: Son más fáciles de escribir.
@option: Permiten usar IPv6.
@correct: Permiten comprobar múltiples direcciones o puertos en una sola regla de forma simultánea, mejorando drásticamente el rendimiento en listas grandes.
@option: Ocupan menos memoria en disco.

@section: Resumen

1.  **Netfilter** es el motor en el Kernel. **iptables/nftables** son los mandos.
2.  **Política por defecto:** Siempre DROP en INPUT y FORWARD. ACCEPT en OUTPUT.
3.  **Stateful:** Siempre permite `ESTABLISHED,RELATED` o romperás la red.
4.  **UFW:** Úsalo si no necesitas cosas complejas (NAT avanzado, QoS). Es seguro y fácil.
5.  **Orden:** Las reglas se leen de arriba a abajo. La primera que coincide gana. Pon las reglas más específicas (bloquear IP X) antes de las generales (permitir todo puerto 80).

Ahora sabes construir un muro impenetrable alrededor de tu infraestructura.