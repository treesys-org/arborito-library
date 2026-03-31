@title: Autenticación Centralizada: LDAP y Kerberos
@icon: 🏛️
@description: Conceptos básicos de gestión de identidad empresarial. Deja de crear usuarios localmente en cada servidor y conéctalos a un directorio central.
@order: 5

# Identidad centralizada: LDAP, Kerberos y SSSD

Cuando administras decenas o cientos de servidores, **crear cuentas locales** (`useradd` en cada máquina) es inviable y no auditable. Los entornos reales usan un **directorio** (LDAP o Active Directory) y protocolos de autenticación (Kerberos) coordinados por un demonio en el cliente: en Linux, habitualmente **SSSD**.

@section: 1. LDAP en una frase

**LDAP** (Lightweight Directory Access Protocol) es un protocolo para consultar y modificar un **árbol de información** (usuarios, grupos, equipos, unidades organizativas). No es una base de datos relacional: está optimizado para **lecturas** y jerarquías.

*   **OpenLDAP** es la implementación libre más citada en documentación genérica.
*   **389 Directory Server**, **FreeIPA** (ahora integrado en el ecosistema IdM de Red Hat) empaquetan LDAP + Kerberos + DNS + políticas.
*   **Active Directory** de Microsoft también expone LDAP; los servidores Linux se unen como miembros o consultan vía **SSSD** + **realmd**.

**DN (Distinguished Name):** ruta única en el árbol, p. ej. `uid=ana,ou=people,dc=empresa,dc=local`.

@section: 2. Kerberos: tickets, no contraseñas en cada petición

**Kerberos** evita reenviar la contraseña por la red en cada servicio. Flujo simplificado:

1.  El usuario autentica contra el **KDC** (Key Distribution Center) y obtiene un **TGT** (Ticket Granting Ticket).
2.  Para acceder a un servicio (NFS, SSH con GSSAPI, HTTP con SPNEGO), presenta tickets derivados sin reenviar el secreto en claro.

**Sincronización de tiempo:** los tickets tienen validez corta; **NTP** mal configurado es la causa número uno de fallos “misteriosos” en Kerberos.

@section: 3. SSSD: el pegamento en el servidor Linux

**SSSD** (`sssd`) resuelve nombres de usuario contra LDAP/AD, obtiene tickets Kerberos y **cachea** credenciales para tolerar caídas temporales del directorio.

Archivos clave (según distribución):

*   `/etc/sssd/sssd.conf` — dominios, URIs LDAP, `krb5.conf` para realm.
*   `authselect` (RHEL) o **PAM** en `/etc/pam.d/sshd` para encadenar SSSD.

**`getent passwd usuario`:** si el usuario viene del directorio, debe aparecer tras configurar SSSD.

**`realm join` (realmd):** en RHEL/Fedora/Debian modernos, une el host a un dominio AD con un comando asistido; revisa siempre políticas de firewall y DNS (SRV).

@section: 4. FreeIPA / IdM (visión de arquitectura)

**FreeIPA** (o **Red Hat Identity Management**) ofrece:

*   LDAP + Kerberos + DNS integrados.
*   **HBAC** (control de acceso a hosts) y **sudo** rules centralizadas.
*   Certificados y políticas de contraseña.

En examen tipo **LPIC-2** se espera que **entiendas** el papel de cada componente, no que memorices cada atributo `ldapsearch`.

@section: 4b. Esquema de `krb5.conf` y `sssd.conf` (conceptual)

**`/etc/krb5.conf`** define el **realm** (dominio Kerberos en mayúsculas, p. ej. `EMPRESA.LOCAL`), los **KDCs** (`kdc = ...`) y políticas de tiempo (`ticket_lifetime`). Un error de mayúsculas/minúsculas en el realm o un KDC mal escrito produce fallos opacos: *“Cannot find KDC for realm”*.

**`/etc/sssd/sssd.conf`** (simplificado) tiene secciones `[sssd]` con `domains`, y `[domain/EMPRESA]` con:

*   `id_provider = ldap` o `ad`
*   `auth_provider = krb5` o `ad`
*   `ldap_uri`, `ldap_search_base`, `krb5_server`, `krb5_realm`

**NSS/PAM:** `sss` debe aparecer en `/etc/nsswitch.conf` (`passwd`, `group`, `shadow`) y en `/etc/pam.d/common-auth` o equivalentes (RHEL usa `authselect`). Sin eso, `getent` nunca verá usuarios del dominio.

@section: 5. Diagnóstico rápido

```bash
# Estado de SSSD
sudo systemctl status sssd

# Logs
sudo journalctl -u sssd -e

# Consulta LDAP manual (OpenLDAP)
ldapsearch -x -H ldap://servidor -b "dc=empresa,dc=local" "(uid=ana)"

# Comprobar realm Kerberos
klist
```

**Errores típicos:** DNS que no resuelve el KDC; reloj desfasado; certificado TLS en LDAP rechazado; firewall bloqueando 389/636/88.

**Tabla de “primeros auxilios”:**

| Síntoma | Comprueba primero |
| :--- | :--- |
| `kinit` falla pero la red va | Hora (`chronyc sources` / `timedatectl`), realm y nombre DNS del KDC |
| `getent passwd usuario` vacío | `sssd` activo, `nsswitch`, `ldap_search_base` |
| Login SSH con usuario de AD falla | PAM, `authselect`/SSSD, logs `journalctl -u sssd` |
| LDAP TLS error | CA en `/etc/ssl` o `ldap_tls_cacert`, reloj |

@section: 6. Laboratorio mental

1.  Enumera tres ventajas de centralizar identidades frente a cuentas locales en 50 servidores.
2.  ¿Por qué Kerberos y LDAP suelen ir juntos en entornos empresariales?
3.  Dibuja en papel el flujo: laptop → SSH → servidor Linux → `sssd` → LDAP/AD para **uid** y Kerberos para **tickets** (sin enviar contraseña en cada petición a todos los servicios).

@quiz: ¿Qué demonio suele usar un servidor Linux para integrar cuentas LDAP/Active Directory con PAM y NSS?
@option: httpd
@correct: sssd (System Security Services Daemon)
@option: cupsd

@quiz: ¿Qué problema provoca casi siempre tickets Kerberos inválidos aunque la contraseña sea correcta?
@option: Disco lleno
@correct: Desincronización horaria entre cliente y KDC (NTP)
@option: Permisos 777 en /tmp

@quiz: ¿Qué comando muestra los tickets Kerberos del usuario actual?
@option: kinit
@correct: klist
@option: ticket-show
