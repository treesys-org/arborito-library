@title: Servidores de Correo: Postfix y Dovecot Basics
@icon: 📧
@description: Introducción al complejo mundo del email. MTA vs MDA, configuración básica de Postfix y por qué enviar emails es difícil hoy en día.
@order: 4

# Correo electrónico en Linux: Postfix, Dovecot y entregabilidad

Operar correo propio es **difícil** no por instalar paquetes, sino por **reputación**, **DNS** y **abuso**. Esta lección te da el modelo mental de **LPIC-2** y de entrevista: MTA, MDA, autenticación SMTP, y SPF/DKIM/DMARC.

@section: 1. Arquitectura: MTA, MDA, MUA

1.  **MUA (Mail User Agent):** cliente del usuario (Thunderbird, mutt).
2.  **MTA (Mail Transfer Agent):** transporta entre servidores vía **SMTP** (puerto 25 entre servidores; 587/465 sometido a autenticación para clientes). **Postfix** es el MTA más habitual en Linux.
3.  **MDA / Mailbox:** entrega local en **Maildir** o **mbox**. **Dovecot** sirve **IMAPS/POP3S** para que el MUA lea el buzón.

Flujo típico: MUA → **sumisión** a Postfix (587) → Postfix entrega a otro MTA (25) o a Dovecot vía **LMTP**.

@section: 2. Postfix: parámetros que no puedes ignorar

Instalación (Debian/Ubuntu): `sudo apt install postfix`. El asistente pregunta tipo de sitio; en laboratorio **Internet Site** con FQDN coherente.

**`/etc/postfix/main.cf` (conceptos):**

*   **`myhostname`:** FQDN público del servidor (`mail.empresa.com`).
*   **`mydestination`:** dominios para los que este servidor **termina** el correo localmente.
*   **`mynetworks`:** redes que pueden **relay** sin autenticación (solo LAN de confianza). **Nunca** `0.0.0.0/0` — serías **open relay**.
*   **TLS:** `smtpd_tls_cert_file`, `smtpd_tls_key_file` o rutas de Let’s Encrypt.
*   **SASL:** para que usuarios remotos envíen correo autenticados (Dovecot como backend SASL es patrón común).

**Colas y diagnóstico:**

```bash
sudo postfix check
mailq
sudo postqueue -f   # reintentar cola
sudo tail -f /var/log/mail.log
```

@section: 3. Dovecot: IMAP y buzones

```bash
sudo apt install dovecot-imapd dovecot-lmtpd
```

*   **Maildir:** `~/Maildir` con subcarpetas `cur`, `new`, `tmp`.
*   Postfix debe pasar correo a Dovecot: **LMTP** (`virtual_transport = lmtp:unix:private/dovecot-lmtp`) según diseño.

**Prueba local:**

```bash
echo "prueba" | mail -s "test" usuario@localhost
```

@section: 4. La entregabilidad moderna (SPF, DKIM, DMARC)

Sin esto, Gmail/Outlook **rechazan** o mandan a spam.

*   **SPF (TXT):** lista IPs/servidores autorizados a enviar para tu dominio.
*   **DKIM:** firma criptográfica por mensaje; clave pública en DNS.
*   **DMARC:** política (`none`, `quarantine`, `reject`) si falla alineación SPF/DKIM.
*   **PTR (rDNS):** la IP del servidor debe resolver a un nombre coherente con `myhostname`.

**Herramientas:** `dig TXT empresa.com`, `opendkim-testkey`, servicios online de comprobación de cabeceras.

@section: 5. RHEL vs Debian

*   **RHEL/Fedora:** paquetes `postfix`, `dovecot`; SELinux booleans (`postfix_*`, `dovecot_*`); **firewalld** puertos 25/587/993.
*   **Debian/Ubuntu:** mismos demonios, rutas `/etc/postfix`, `/etc/dovecot`; **ufw** equivalente.

@section: 5b. Relay, colas y políticas anti-abuso

**Open relay** (aceptar correo de Internet para cualquier destino) te convierte en **spam cannon** en minutos. Postfix combate esto con:

*   **`smtpd_recipient_restrictions`** / **`smtpd_relay_restrictions`**: orden importa; típicamente `permit_mynetworks`, `permit_sasl_authenticated`, `reject_unauth_destination`.
*   **Greylisting** y **RBLs** (listas negras DNS) son capas externas opcionales.

**Colas atascadas:** `mailq` muestra IDs; `postsuper -d ALL` borra cola en emergencia (¡pierdes correo!); `postcat -q <id>` inspecciona un mensaje concreto. Si el disco está lleno, Postfix deja de aceptar: mira `df` y `/var/spool/postfix`.

**Cabeceras:** `Received:`, `Authentication-Results:`, `DKIM-Signature` — aprende a leerlas para saber si el fallo es **antes** o **después** de tu servidor.

@section: 6. Caso práctico guiado (laboratorio)

1.  Instala Postfix y Dovecot en VMs de prueba.
2.  Configura **solo** recepción local y comprueba con `swaks` o `telnet` al puerto 25 desde otra VM.
3.  Añade registros SPF/DKIM de prueba en un DNS de laboratorio y verifica con `dig`.
4.  Simula cola bloqueada: detén el DNS saliente o llena `/var` y observa cómo cambia `mailq` y los logs.

@section: 7. Expectativas realistas (2026)

Operar **correo saliente** fiable hacia Gmail/Outlook exige IP limpia, PTR, SPF/DKIM/DMARC alineados, y a veces **warm-up** de reputación. Muchas organizaciones delegan el relay saliente en **SendGrid**, **Amazon SES**, **Mailgun**, etc., y solo mantienen **recepción** interna. No es derrota: es **separación de responsabilidades**.

@quiz: ¿Qué riesgo implica `mynetworks` demasiado amplio en Postfix?
@option: Cifrado TLS débil
@correct: Open relay: terceros envían spam usando tu servidor
@option: IMAP lento

@quiz: ¿Qué registro DNS indica qué hosts pueden enviar correo en nombre de un dominio?
@option: MX
@correct: SPF (registro TXT)
@option: SRV

@quiz: ¿Qué protocolo usa normalmente el cliente Thunderbird para leer correo en el servidor?
@option: SMTP puerto 25
@correct: IMAPS (143/993) o POP3S
@option: LDAP
