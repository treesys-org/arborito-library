@title: Servidor DNS: Configuración de BIND9
@icon: 🌐
@description: Deja de depender de Google. Monta tu propio servidor de nombres autoritativo para tu red interna con BIND9. Zonas, registros A, CNAME y Forwarders.
@order: 2

# BIND 9: zonas directas, inversas y resolución recursiva

Un servidor **autoritativo** responde por dominios que administras; un **resolutor recursivo** (o **forwarder**) reenvía lo que no es local. **BIND** sigue siendo referencia en documentación **LPIC-2** y en muchas empresas.

@section: 1. Conceptos imprescindibles

*   **Zona:** fragmento del árbol DNS por el que eres responsable.
*   **Registros:** **A/AAAA**, **CNAME**, **MX**, **NS**, **SRV**, **TXT** (SPF).
*   **SOA:** serial, tiempos de refresco y expiración; **incrementa el serial** en cada cambio (formato `YYYYMMDDnn` habitual).
*   **Zona inversa (PTR):** mapa IP → nombre; en IPv4 suele estar bajo `in-addr.arpa`.

@section: 2. Instalación y layout (Debian)

```bash
sudo apt install bind9 bind9utils
```

Archivos típicos:

*   `/etc/bind/named.conf.options` — opciones globales, forwarders, `allow-query`.
*   `/etc/bind/named.conf.local` — zonas del sitio.
*   `/etc/bind/db.*` — datos de zona.

**RHEL:** paquete `bind`, rutas `/etc/named.conf`, `/var/named/`.

@section: 3. Zona directa (ejemplo)

`/etc/bind/named.conf.local`:

```text
zone "empresa.local" {
    type master;
    file "/etc/bind/db.empresa.local";
};
```

`/etc/bind/db.empresa.local` (adapta SOA y serial):

```text
$TTL 604800
@   IN SOA ns1.empresa.local. admin.empresa.local. (
        2026032901 ; Serial
        604800     ; Refresh
        86400      ; Retry
        2419200    ; Expire
        604800 )   ; Negative TTL
; 
    IN NS     ns1.empresa.local.
ns1 IN A      192.168.1.10
www IN CNAME  ns1
```

@section: 4. Zona inversa (PTR) para laboratorio

Para `192.168.1.0/24`, la zona inversa es `1.168.192.in-addr.arpa`:

```text
zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.1";
};
```

```text
10    IN PTR ns1.empresa.local.
```

**Importante:** muchos proveedores gestionan PTR de IPs públicas; en LAN lo controlas tú.

@section: 5. Forwarders y recursión

En `named.conf.options`:

```text
options {
    directory "/var/cache/bind";
    forwarders { 1.1.1.1; 8.8.8.8; };
    forward only;
    dnssec-validation auto;
};
```

**No abrir recursión** a Internet sin control — riesgo de **DNS amplificación** en ataques. Restringe `allow-query` a tu LAN o usa `views`.

@section: 6. DNSSEC (visión)

Firmas criptográficas para integridad de respuestas. BIND puede **firmar** zonas (`dnssec-signzone`) o usar **inline signing**. Para examen conceptual: entiende **KSK/ZSK** y **chain of trust**.

@section: 7. Diagnóstico

```bash
sudo named-checkconf
sudo named-checkzone empresa.local /etc/bind/db.empresa.local
sudo systemctl reload bind9
dig @192.168.1.10 www.empresa.local
dig -x 192.168.1.10 @192.168.1.10
```

@section: 8. Alternativa: Unbound (solo recursivo)

**Unbound** es ideal para **caché** en el borde sin zonas autoritativas. No mezcles conceptos en exámenes: **autoritativo vs recursivo**.

@section: 9. Laboratorio

1.  Levanta una zona interna y un forwarder; comprueba con `dig +trace` externo vs interno.
2.  Rompe un serial y observa que los esclavos (si los hubiera) no actualizan.

@quiz: ¿Qué campo del SOA debes incrementar al editar registros en la zona?
@option: TTL únicamente
@correct: Serial
@option: Expire

@quiz: ¿Qué tipo de registro suele usarse para alias de nombre (www → host canónico)?
@option: MX
@correct: CNAME
@option: TXT

@quiz: ¿Qué comando valida la sintaxis de un archivo de zona BIND?
@option: named-checkconf solo
@correct: named-checkzone
@option: dig -x
