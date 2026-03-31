@title: Gestión de Certificados SSL/TLS (OpenSSL)
@icon: 📜
@description: Cifrado para todos. Entiende la diferencia entre CSR, CRT y Key. Genera certificados autofirmados y usa Certbot para HTTPS gratuito.
@order: 3

# TLS en Linux: OpenSSL, PKI privada y Let’s Encrypt

Los certificados **X.509** son la base de **HTTPS**, **LDAPS**, **SMTPS** e **IMAPS**. En **LPIC-2** y en entrevistas debes explicar: clave privada, cadena de confianza, CSR, y renovación automatizada.

@section: 1. Piezas de un despliegue TLS

*   **Clave privada (`.key`):** secreto del servidor; permisos `0600` y propietario root o servicio.
*   **Certificado público (`.crt` / `.pem`):** contiene clave pública + metadatos + firma de la CA.
*   **Cadena / intermediate:** enlaza tu certificado con una **CA raíz** confiable en navegadores.
*   **CSR (Certificate Signing Request):** solicitud que envías a una CA pública o interna; **no** contiene la clave privada.

@section: 2. OpenSSL: comandos de supervivencia

**Autofirmado (laboratorio):**

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout servidor.key -out servidor.crt -subj "/CN=lab.local"
```

**Generar CSR para CA corporativa:**

```bash
openssl req -new -newkey rsa:2048 -nodes \
  -keyout dominio.key -out dominio.csr
```

**Inspeccionar certificado:**

```bash
openssl x509 -in servidor.crt -text -noout
```

**Verificar cadena contra archivo CA:**

```bash
openssl verify -CAfile ca-chain.pem servidor.crt
```

@section: 3. Let’s Encrypt y Certbot

CA pública gratuita con validez **~90 días** — la renovación **automática** es obligatoria.

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d www.ejemplo.com
```

**Renovación:** `certbot renew` (systemd timer o cron). Prueba con `certbot renew --dry-run`.

**Desafío HTTP-01:** Certbot coloca un token bajo la webroot; el puerto 80 debe ser alcanzable públicamente (o usa **DNS-01** con plugin de tu proveedor).

**DNS-01** es imprescindible cuando:

*   El sitio no tiene puerto 80 abierto.
*   Necesitas certificados para **nombres internos** o **wildcard** (`*.ejemplo.com`).
*   El balanceador termina TLS y el origin no recibe el desafío HTTP.

Requiere API del proveedor DNS (credenciales en `/etc/letsencrypt/` con permisos estrictos).

**TLS moderno:** versiones mínimas **1.2** (1.3 preferido), suites fuertes, **OCSP stapling** en Nginx/Apache para reducir latencia y fugas de privacidad. `openssl s_client -connect host:443 -tls1_2` prueba compatibilidad.

@section: 4. PKI interna (empresa)

Muchas empresas usan **CA interna** (OpenSSL, **cfssl**, **step-ca**). Debes **distribuir** el certificado raíz a estaciones de confianza (almacén del SO/navegador o perfil MDM).

**openssl ca** o herramientas dedicadas gestionan revocación (**CRL**, **OCSP**).

@section: 5. Diferencias RHEL / Debian

*   Rutas típicas: `/etc/pki/tls` (RHEL), `/etc/ssl` (Debian).
*   **certmonger** o **getcert** en RHEL puede renovar certificados IPA/FreeIPA.
*   SELinux: contextos correctos en archivos bajo `/etc/nginx` o `/etc/httpd`.

@section: 6. Errores habituales

*   Cadena incompleta (falta intermediate) → warnings en navegador.
*   Certificado caducado → monitoreo con **Icinga**, **Prometheus blackbox**, etc.
*   Clave mundialmente legible → fallo de auditoría.

@section: 7. Laboratorio ampliado

1.  Crea un autofirmado y configúralo en **nginx** o **apache** en VM.
2.  Ejecuta `openssl s_client -connect localhost:443 -servername lab.local` y analiza el handshake (cadena, versión TLS, algoritmo de firma).
3.  Monta **Certbot** en un dominio de prueba o usa `--staging` para no agotar límites; revisa el **timer** systemd: `systemctl list-timers | grep certbot`.
4.  Documenta qué harías si el certificado expira en producción: rotación de emergencia, comunicación a clientes API, y verificación de **HSTS** (no bloquear usuarios con un certificado equivocado).

@section: 8. Checklist antes de ir a producción

*   Cadena completa (leaf + intermediate) servida por el terminador TLS.
*   Clave privada con permisos correctos; servicio corre como usuario sin privilegios donde sea posible.
*   Renueva antes de 30 días de caducidad; alertas en monitorización.
*   Prueba de `openssl verify` y de navegador desde red externa (no solo localhost).

@quiz: ¿Qué archivo nunca debe exponerse públicamente?
@option: CSR
@option: Certificado .crt
@correct: Clave privada (.key)
@option: Cadena intermediate

@quiz: ¿Qué herramienta cliente de Let’s Encrypt suele modificar la configuración de Nginx automáticamente?
@option: openssl ca
@correct: certbot con plugin --nginx
@option: ssh-copy-id

@quiz: ¿Por qué Let’s Encrypt requiere procesos de renovación automática?
@option: Porque prohíbe TLS 1.2
@correct: Porque los certificados tienen vida corta (~90 días)
@option: Porque solo funciona en Windows
