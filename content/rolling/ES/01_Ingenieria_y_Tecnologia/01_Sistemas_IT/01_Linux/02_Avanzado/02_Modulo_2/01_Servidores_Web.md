@title: Apache y Nginx: virtual hosts, TLS y proxy inverso
@icon: 🌐
@description: Desplegar y endurecer servidores HTTP: MPM/event, virtual hosts, HTTPS, cabeceras de seguridad y rol de proxy y balanceo.
@order: 1

# Servidores web en Linux: Apache, Nginx y buenas prácticas

Aprenderás a desplegar **Apache y Nginx** con **virtual hosts**, **HTTPS** (certificados y cabeceras de seguridad), modelos **MPM/event** y cuándo usar **proxy inverso** o balanceo frente a cargas reales y exámenes tipo LPIC-2 / RHCE.

@section: Mapa LPIC-2 — Módulo 2 (servicios de red y almacenamiento)

Referencia **LPIC-2** (bloques 207/208/210 según versión) y despliegue tipo **RHCE**:

*   **207 — Servidor web:** Apache/Nginx, virtual hosts, TLS, hardening, proxy inverso.
*   **208 — Correo:** MTA/MDA (Postfix/Dovecot) y DNS de correo (SPF/DKIM/DMARC) en lección dedicada.
*   **209 — Compartición de archivos:** NFS/Samba en lección de compartición.
*   **210 — Bases de datos:** MariaDB/PostgreSQL operativos.
*   **RHEL:** `httpd` + `firewalld` + SELinux booleans (`httpd_can_network_connect`); **Debian/Ubuntu:** `apache2`/`nginx` + `ufw`; siempre validar contexto.

Bienvenido a la ingeniería de la web.

En el nivel junior, aprendiste a instalar Apache y ver la página "It Works!". En el nivel avanzado, eso no sirve. Un servidor web mal configurado es un cuello de botella, un riesgo de seguridad y un desperdicio de recursos.

En este módulo masivo, no solo vamos a configurar servidores; vamos a entender cómo gestionan las conexiones a nivel de kernel, cómo optimizar la memoria RAM, cómo asegurar el handshake SSL/TLS y cómo escalar cuando tienes 10.000 usuarios simultáneos.

@section: 1. Historia y Arquitectura: El Problema C10K

Para entender por qué usamos Nginx hoy en día, debemos entender la historia.

### La Era de los Procesos (Apache 1.0)
En los años 90, la web era simple. Apache dominaba. Su modelo era sencillo:
1.  Llega una petición (Cliente A).
2.  Apache crea un **Proceso** nuevo (o usa uno de un pool).
3.  Ese proceso atiende al Cliente A y solo al Cliente A hasta que termina.
4.  El proceso muere o vuelve al pool.

Esto es robusto (si un proceso falla, no afecta a los demás), pero es **pesado**. Un proceso consume mucha RAM. Si tienes 100 visitas, necesitas 100 procesos.

### El Problema C10K (1999)
Dan Kegel planteó el problema: *"¿Cómo diseñamos un servidor que soporte 10.000 (10K) clientes conectados a la vez?"*.
Con el modelo de procesos de Apache, si cada proceso ocupa 2MB de RAM y tienes 10.000 clientes:
`10.000 * 2MB = 20.000 MB = 20 GB de RAM`.
En 1999, eso era imposible. El servidor colapsaba por falta de memoria o por el coste de la CPU cambiando de contexto entre 10.000 procesos (Context Switching).

### La Solución Asíncrona (Nginx)
Igor Sysoev creó Nginx para resolver esto. Su arquitectura es **Event-Driven** (Guiada por eventos) y **Asíncrona**.
1.  Un solo proceso ("Worker") atiende a miles de clientes.
2.  No se bloquea. Si el Cliente A está descargando un archivo lento, Nginx no espera mirando; pasa a atender al Cliente B, C y D mientras los datos del A fluyen por la red.
3.  Usa llamadas al sistema no bloqueantes (`epoll` en Linux, `kqueue` en BSD).

**Resultado:** Nginx puede mantener 10.000 conexiones activas usando apenas unos MB de RAM.

@quiz: ¿Cuál es la diferencia fundamental en el uso de memoria entre el modelo clásico de procesos (Apache Prefork) y el modelo asíncrono (Nginx)?
@option: Apache usa menos memoria porque sus procesos son más pequeños.
@correct: Apache escala linealmente (más clientes = más RAM), mientras que Nginx mantiene un uso de RAM casi constante y bajo incluso con miles de conexiones.
@option: Nginx usa más memoria porque guarda todo en caché.
@option: No hay diferencia, depende del sistema operativo.

@section: 2. Apache HTTPD: El Gigante Modular

Aunque Nginx es más rápido para contenido estático, Apache sigue siendo el rey de la **Flexibilidad**. Su sistema de módulos dinámicos y los archivos `.htaccess` permiten configuraciones complejas por directorio sin reiniciar el servidor.

### Los MPMs (Multi-Processing Modules)
Apache evolucionó. Ahora puedes elegir su motor interno. Es vital que sepas cuál estás usando.

1.  **MPM Prefork:**
    *   El clásico. Un proceso por petición. Thread-safe (seguro para librerías viejas de PHP).
    *   *Uso:* Si usas `mod_php` antiguo. Lento y consume mucha RAM.
2.  **MPM Worker:**
    *   Híbrido. Múltiples procesos, y cada proceso tiene múltiples Hilos (Threads).
    *   *Ventaja:* Menos RAM que Prefork.
3.  **MPM Event:**
    *   El moderno (Estándar en Apache 2.4+). Similar a Worker pero optimizado para conexiones Keep-Alive. Delega las conexiones vivas a hilos dedicados para liberar a los trabajadores.
    *   *Uso:* El estándar para producción hoy en día.

**Verificar tu MPM:**
```bash
$ apachectl -V | grep MPM
Server MPM:     event
```

### Configuración Avanzada (`apache2.conf` / `httpd.conf`)
Vamos a diseccionar una configuración real de producción para evitar ataques y mejorar rendimiento.

#### Ocultar Información (Security by Obscurity)
Por defecto, Apache grita su versión y tu SO al mundo en las cabeceras HTTP.
`Server: Apache/2.4.41 (Ubuntu)`
Esto ayuda a los hackers a buscar exploits específicos.

Edita `/etc/apache2/conf-enabled/security.conf`:
```apache
# Muestra solo "Apache", nada más.
ServerTokens Prod
# No muestra información en páginas de error generadas por el servidor.
ServerSignature Off
# Evita que se trace la ruta interna del servidor (ETags).
FileETag None
```

#### Optimización de KeepAlive
KeepAlive mantiene la conexión abierta para que el navegador baje las imágenes, CSS y JS sin abrir una conexión TCP nueva para cada archivo (lo cual es lento).
Pero si se deja mucho tiempo, consume recursos.

```apache
# En apache2.conf
KeepAlive On
# Cuántas peticiones por conexión (100 es buen número)
MaxKeepAliveRequests 100
# Segundos de espera. Por defecto 5 es suficiente. 15 es demasiado.
KeepAliveTimeout 5
```

#### Desactivar .htaccess (Rendimiento Extremo)
Si controlas el servidor completo, **desactiva los archivos .htaccess**.
¿Por qué? Porque si permites `.htaccess`, Apache tiene que buscar ese archivo en CADA carpeta y subcarpeta cada vez que alguien pide un archivo, "por si acaso" existe y cambia la configuración. Eso son miles de lecturas a disco inútiles (I/O).

Mueve la configuración al archivo principal y desactívalo:
```apache
<Directory />
    AllowOverride None
    Require all denied
</Directory>
```

@quiz: ¿Por qué desactivar `AllowOverride` (archivos .htaccess) mejora el rendimiento de Apache?
@option: Porque los archivos .htaccess consumen mucha CPU al procesarse.
@correct: Porque evita que Apache tenga que buscar recursivamente la existencia de archivos .htaccess en cada directorio de la ruta solicitada, reduciendo operaciones de E/S.
@option: Porque .htaccess es incompatible con HTTP/2.

@section: 3. Nginx: El Maestro del Proxy Inverso

Nginx se usa hoy en día principalmente como **Frontend**. Se pone delante de todo (Node.js, Python, Java, e incluso delante de Apache).

### Anatomía de `nginx.conf`
La configuración de Nginx es jerárquica y basada en bloques.

```nginx
user www-data;
worker_processes auto; # Usa todos los núcleos de CPU disponibles
pid /run/nginx.pid;

events {
    worker_connections 1024; # Conexiones simultáneas por núcleo
    # Total conexiones posibles = worker_processes * worker_connections
    # Si tienes 4 núcleos, soportarás 4096 conexiones.
    multi_accept on;
}

http {
    ##
    # Basic Settings
    ##
    sendfile on; # Usa la llamada al sistema sendfile() para copiar datos en kernel (Zero Copy)
    tcp_nopush on; # Optimiza el envío de paquetes TCP
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # Logging
    ##
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings (Compresión)
    ##
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6; # Nivel 1-9. 6 es el mejor balance CPU/Tamaño
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

### Server Blocks y Prioridad de Locations
Nginx decide qué bloque ejecutar basándose en reglas muy específicas. Es fuente de confusión común.

Reglas de prioridad de `location`:
1.  `location = /path`: Coincidencia exacta. **Máxima prioridad**.
2.  `location ^~ /path`: Coincidencia de prefijo preferente.
3.  `location ~ /path`: Coincidencia con Expresión Regular (Regex). El primero que coincida en el archivo gana.
4.  `location /path`: Coincidencia de prefijo estándar.

**Ejemplo Trampa:**
```nginx
location /images/ {
    # Bloque A
}

location ~ \.(jpg|png)$ {
    # Bloque B
}
```
Si pides `/images/foto.jpg`:
*   Coincide con Bloque A (prefijo).
*   Coincide con Bloque B (Regex).
*   **GANA EL BLOQUE B** (Las Regex ganan a los prefijos estándar a menos que uses `^~`).

### Nginx como Balanceador de Carga
Nginx puede repartir tráfico entre varios servidores backend.

```nginx
upstream mi_cluster_backend {
    least_conn; # Envía al servidor con menos conexiones activas (inteligente)
    server 10.0.0.1:3000 weight=3; # Recibe el triple de tráfico (servidor potente)
    server 10.0.0.2:3000;
    server 10.0.0.3:3000 backup; # Solo se usa si los otros dos caen
}

server {
    listen 80;
    server_name miweb.com;

    location / {
        proxy_pass http://mi_cluster_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
Esto convierte a tu servidor Nginx en un **Load Balancer** de nivel empresarial.

@quiz: En Nginx, tienes un bloque `location /static/` y otro `location ~ \.css$`. Si solicitas `/static/estilo.css`, ¿cuál se ejecutará por defecto?
@option: location /static/
@correct: location ~ \.css$ (Las expresiones regulares tienen prioridad sobre los prefijos simples)
@option: Ninguno, dará error 404.
@option: El que esté escrito primero en el archivo.

@section: 4. HTTPS y Hardening SSL/TLS

Tener el candado verde no es suficiente. Puedes tener HTTPS y ser vulnerable si usas protocolos viejos (SSLv3, TLS 1.0) o cifrados débiles.

### Generando parámetros Diffie-Hellman fuertes
Por defecto, los servidores usan parámetros de 1024 bits que agencias de inteligencia pueden romper. Genera uno de 2048 o 4096 bits único para tu servidor.
```bash
$ sudo openssl dhparam -out /etc/nginx/dhparam.pem 2048
```
*(Esto tardará un rato. Ve a por café).*

### Configuración Nginx Blindada (Mozilla Modern Config)
No inventes la configuración. Usa los estándares de Mozilla.

```nginx
ssl_certificate /etc/letsencrypt/live/miweb.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/miweb.com/privkey.pem;

# Protocolos: Solo TLS 1.2 y 1.3. Matamos SSL y TLS 1.0/1.1
ssl_protocols TLSv1.2 TLSv1.3;

# Cifrados preferidos (Cipher Suites)
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
ssl_prefer_server_ciphers off;

# HSTS (Strict Transport Security)
# Le dice al navegador: "Durante 1 año, NUNCA intentes conectar por HTTP a esta web, fuerza HTTPS siempre".
add_header Strict-Transport-Security "max-age=63072000" always;

# Usar nuestro DHparam fuerte
ssl_dhparam /etc/nginx/dhparam.pem;

# OCSP Stapling (Mejora la velocidad de verificación del certificado)
ssl_stapling on;
ssl_stapling_verify on;
```

@section: 5. Troubleshooting: Cuando la Web Cae

El servidor da "500 Internal Server Error" o "502 Bad Gateway". ¿Qué haces?

**1. Verifica el estado del servicio:**
`systemctl status nginx`
Si está "failed", mira por qué.

**2. Verifica la sintaxis:**
Antes de reiniciar Nginx tras un cambio, SIEMPRE haz:
`sudo nginx -t`
Si hay un error de sintaxis (falta un punto y coma), te lo dirá aquí y no tumbarás el servidor.

**3. Los Logs (La verdad absoluta):**
Si es un error 500 (Aplicación), mira el log de error de tu aplicación o de Apache/Nginx.
`tail -f /var/log/nginx/error.log`
Si es un error 502 (Bad Gateway), significa que Nginx funciona bien, pero el Backend (PHP-FPM, Node.js, Python) está caído o no responde. No culpes a Nginx. Culpa al backend.

**4. Permisos:**
Un clásico en Apache/Nginx.
El usuario `www-data` necesita permiso de **Lectura** en los archivos y de **Ejecución** en las carpetas para poder entrar.
Si `ls -l` muestra que los archivos son `root:root 700`, Nginx no podrá leerlos y dará "403 Forbidden".
Arreglo rápido:
`chown -R www-data:www-data /var/www/html`

@quiz: Estás configurando un servidor y recibes un error "502 Bad Gateway" en el navegador. ¿Qué significa esto más probablemente?
@option: Que Nginx está mal configurado y no arranca.
@option: Que el certificado SSL ha caducado.
@correct: Que Nginx está funcionando y ha recibido la petición, pero el servicio backend al que intenta conectar (PHP, Node, Python) está caído o devolviendo errores.
@option: Que tu conexión a internet se ha cortado.

@section: Resumen del Módulo

1.  **Apache** es modular, flexible, usa `.htaccess`, ideal para hosting compartido. Usa MPM Event en producción.
2.  **Nginx** es asíncrono, ligero, ideal para contenido estático, proxy inverso y cargas altas (C10K).
3.  **Configuración:** No uses configuraciones por defecto. Oculta versiones, activa Gzip, ajusta KeepAlive.
4.  **Seguridad:** Desactiva protocolos viejos (SSLv3, TLS1.0). Usa HSTS.
5.  **Diagnóstico:** `nginx -t` antes de recargar. Logs para entender errores 5xx.

Ahora tienes el conocimiento para montar la infraestructura de una startup tecnológica.