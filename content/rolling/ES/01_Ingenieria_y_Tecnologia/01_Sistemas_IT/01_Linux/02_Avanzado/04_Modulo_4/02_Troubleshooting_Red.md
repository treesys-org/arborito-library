@title: Troubleshooting de Red: tcpdump y Wireshark CLI
@icon: 🔬
@description: Cuando el ping no basta. Captura paquetes en tiempo real, analiza el tráfico y descubre por qué la conexión se corta.
@order: 2

# Diagnóstico de red avanzado: tcpdump, ss, mtr y trazas

Cuando “hay red” pero el servicio falla, necesitas **evidencia**: capturas de paquetes, estado de sockets y rutas. Esta lección complementa **ping/traceroute** con herramientas de **LPIC-2** y de entorno productivo.

@section: 1. tcpdump: captura en la terminal

```bash
sudo tcpdump -i eth0 -n host 192.168.1.50 and port 443
sudo tcpdump -i any -w captura.pcap -c 1000
```

*   `-n` evita resolución DNS inversa.
*   `-w` guarda para **Wireshark** offline.
*   Filtros BPF: `tcp`, `udp`, `host`, `port`, `net`.

**Handshake TCP:** SYN → SYN-ACK → ACK. Si solo ves SYN retransmitidos, **no hay respuesta** (firewall DROP, ruta incorrecta, host caído). **RST** indica rechazo explícito.

@section: 2. ss (reemplazo de netstat)

```bash
ss -tlnp        # sockets TCP en escucha con proceso
ss -tanp        # todas las conexiones TCP con timers
ss -s           # resumen
```

**`ss`** usa **netlink** y es más rápido que `netstat` legacy.

@section: 3. mtr (my traceroute)

Combina **ping** y **traceroute** con estadísticas por salto:

```bash
mtr -rwzbc 100 ejemplo.com
```

Útil para **pérdida intermitente** en WAN.

@section: 4. dig / nslookup y resolución DNS

```bash
dig +trace ejemplo.com
dig @8.8.8.8 A api.ejemplo.com
```

Si la aplicación falla pero `curl` a IP funciona, sospecha **DNS** (systemd-resolved, `/etc/resolv.conf`, search domains).

@section: 5. Conectividad de capa de aplicación

```bash
curl -vI https://servidor
openssl s_client -connect host:443 -servername host
nc -vz host 5432
```

**TLS:** cadena incompleta, SNI incorrecto, certificado caducado — `openssl` lo muestra.

@section: 5b. MTU, “black holes” y Path MTU Discovery

Un síntoma clásico: **SSH se cuelga tras el login**, **HTTPS carga a medias**, **SMB funciona con paquetes pequeños**. A veces un túnel **VPN** o un router mal configurado fragmenta mal o bloquea **ICMP Fragmentation Needed**. Prueba:

```bash
ping -M do -s 1400 ejemplo.com    # no fragmentar; reduce -s hasta que pase
tracepath -n ejemplo.com
```

Ajustar **MTU** en la interfaz o en el cliente VPN suele ser la curación. `tcpdump` mostrará retransmisiones sin ACK si el problema es de red intermedia.

@section: 5c. Tabla de conntrack y nf_tables

En firewalls stateful, agotar **conntrack** (`nf_conntrack: table full`) provoca drops silenciosos. Revisa:

```bash
cat /proc/sys/net/netfilter/nf_conntrack_count
sudo conntrack -L | wc -l    # si está instalado
```

Con **nftables**: `sudo nft list ruleset` y contadores; con **iptables** legacy: `iptables-save | less`.

@section: 6. Namespaces y firewalls (recordatorio)

Si `iptables`/`nftables` o **firewalld** bloquean, tcpdump puede mostrar tráfico **antes** del filtro en interfaces; interpreta **INPUT/OUTPUT** vs **FORWARD**.

@section: 7. Flujo de trabajo forense

1.  Reproduce el problema con un cliente conocido.
2.  **Simultáneamente:** `ss` en servidor, `tcpdump` en cliente o servidor.
3.  Guarda **pcap** y adjunta a ticket; abre en Wireshark filtrando por **tcp.stream**.

@section: 8. Laboratorio

1.  Captura una negociación TLS a un sitio público (`tcpdump port 443`) y observa el handshake en Wireshark.
2.  Compara salida de `ss -tn` antes y después de arrancar un servicio web.
3.  Fuerza un MTU bajo en una VM (`ip link set dev eth0 mtu 1200`) y observa el efecto en descargas grandes vs `curl` pequeño.
4.  Con `nft` o `iptables`, cuenta reglas DROP y relaciona con retransmisiones en `tcpdump`.

@quiz: ¿Qué herramienta moderna sustituye a netstat para listar sockets con procesos asociados?
@option: ifconfig
@correct: ss
@option: route

@quiz: ¿Qué patrón en tcpdump sugiere firewall que descarta silenciosamente paquetes?
@option: SYN seguido de RST inmediato
@correct: SYN repetidos sin SYN-ACK
@option: ICMP echo reply

@quiz: ¿Para qué sirve `openssl s_client` en diagnóstico?
@option: Solo generar claves
@correct: Inspeccionar handshakes TLS y certificados en vivo
@option: Configurar firewall
