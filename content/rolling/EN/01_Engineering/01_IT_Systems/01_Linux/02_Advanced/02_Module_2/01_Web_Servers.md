
@title: Web Servers: Apache and Nginx Bible
@icon: 🌐
@description: Exhaustive web engineering guide. Architectures (Prefork vs Event), C10K problem, high performance config, SSL/TLS Hardening, Reverse Proxy, and Load Balancing.
@order: 1

# Web Titans: Architecture and Performance

@section: LPIC-2 map — Module 2 (web, mail, file sharing, databases)

Covers **LPIC-2** network service objectives: Apache/Nginx, mail stack (separate lesson), NFS/Samba, DB servers.

**RHEL:** `httpd`, `firewalld`, SELinux booleans for web; **Debian/Ubuntu:** `apache2`/`nginx`, `ufw`.

Welcome to web engineering.

Junior level, installed Apache "It Works!". Advanced level, useless. Server bad config bottleneck, security risk, waste resources.

Massive module, configure servers; understand manage connections kernel level, optimize RAM, secure SSL/TLS handshake, scale 10,000 users.

@section: 1. History and Architecture: C10K Problem

Understand Nginx today, understand history.

### Process Era (Apache 1.0)
90s, web simple. Apache dominated. Model:
1.  Request arrives (Client A).
2.  Apache creates **Process** new (or pool).
3.  Process serves Client A only Client A finish.
4.  Process dies pool.

Robust (process fail, others safe), **heavy**. Process RAM. 100 visits, 100 processes.

### C10K Problem (1999)
Dan Kegel problem: *"How server support 10,000 (10K) clients simultaneous?"*.
Process model Apache, 2MB RAM process 10,000 clients:
`10,000 * 2MB = 20,000 MB = 20 GB RAM`.
1999, impossible. Server collapse memory CPU Context Switching.

### Asynchronous Solution (Nginx)
Igor Sysoev Nginx solve. Architecture **Event-Driven** **Asynchronous**.
1.  Single process ("Worker") thousands clients.
2.  Non-blocking. Client A download slow, Nginx wait; attend Client B, C, D while A data flows network.
3.  Use non-blocking system calls (`epoll` Linux, `kqueue` BSD).

**Result:** Nginx 10,000 connections MBs RAM.

@quiz: Fundamental difference memory classic process model (Apache Prefork) asynchronous (Nginx)?
@option: Apache uses less memory processes smaller.
@correct: Apache scales linear (more clients RAM), Nginx memory constant low thousands connections.
@option: Nginx uses more memory cache.
@option: No difference, OS.

@section: 2. Apache HTTPD: Modular Giant

Nginx faster static, Apache king **Flexibility**. Dynamic modules `.htaccess` complex per-directory config restart less.

### MPMs (Multi-Processing Modules)
Apache evolved. Choose internal engine. Vital know.

1.  **MPM Prefork:**
    *   Classic. One process request. Thread-safe (old PHP libs).
    *   *Use:* `mod_php` old. Slow RAM heavy.
2.  **MPM Worker:**
    *   Hybrid. Multiple processes, process multiple Threads.
    *   *Advantage:* Less RAM Prefork.
3.  **MPM Event:**
    *   Modern (Standard Apache 2.4+). Like Worker optimized Keep-Alive. Delegate connections threads dedicated free workers.
    *   *Use:* Production standard.

**Verify MPM:**
```bash
$ apachectl -V | grep MPM
Server MPM:     event
```

### Advanced Config (`apache2.conf` / `httpd.conf`)
Dissect real production config avoid attacks performance.

#### Hide Info (Security by Obscurity)
Default, Apache screams version OS headers.
`Server: Apache/2.4.41 (Ubuntu)`
Hackers exploits.

Edit `/etc/apache2/conf-enabled/security.conf`:
```apache
# Show "Apache", nothing else.
ServerTokens Prod
# No info error pages.
ServerSignature Off
# Avoid internal server path (ETags).
FileETag None
```

#### KeepAlive Optimization
KeepAlive connection open browser download images, CSS, JS no open TCP new (slow).
Long time, resource.

```apache
# In apache2.conf
KeepAlive On
# Requests per connection (100 good)
MaxKeepAliveRequests 100
# Seconds wait. 5 sufficient. 15 too much.
KeepAliveTimeout 5
```

#### Disable .htaccess (Extreme Performance)
Control server, **disable .htaccess**.
Why? Allow `.htaccess`, Apache search file EVERY folder subfolder request, "case" exists change config. Thousands disk reads (I/O).

Move config main file disable:
```apache
<Directory />
    AllowOverride None
    Require all denied
</Directory>
```

@quiz: Why disabling `AllowOverride` (.htaccess) improve Apache performance?
@option: .htaccess CPU heavy.
@correct: Avoids Apache search recursive .htaccess existence directory request, reducing I/O.
@option: .htaccess incompatible HTTP/2.

@section: 3. Nginx: Reverse Proxy Master

Nginx used **Frontend**. Front everything (Node.js, Python, Java, Apache).

### Anatomy `nginx.conf`
Config hierarchical blocks.

```nginx
user www-data;
worker_processes auto; # All CPU cores
pid /run/nginx.pid;

events {
    worker_connections 1024; # Connections per core
    # Total connections = worker_processes * worker_connections
    # 4 cores, 4096 connections.
    multi_accept on;
}

http {
    ##
    # Basic Settings
    ##
    sendfile on; # Syscall sendfile() kernel copy (Zero Copy)
    tcp_nopush on; # Optimize TCP packet send
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
    # Gzip Settings
    ##
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6; # 1-9. 6 balance CPU/Size
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

### Server Blocks Priority Locations
Nginx decides block execute rules specific. Confusion.

Priority `location`:
1.  `location = /path`: Exact. **Max priority**.
2.  `location ^~ /path`: Prefix preferential.
3.  `location ~ /path`: Regex. First match wins.
4.  `location /path`: Prefix standard.

**Trap Example:**
```nginx
location /images/ {
    # Block A
}

location ~ \.(jpg|png)$ {
    # Block B
}
```
Request `/images/foto.jpg`:
*   Match A (prefix).
*   Match B (Regex).
*   **WINS B** (Regex wins standard prefix unless `^~`).

### Nginx Load Balancer
Distribute traffic backends.

```nginx
upstream my_cluster_backend {
    least_conn; # Send server least connections (smart)
    server 10.0.0.1:3000 weight=3; # Triple traffic (powerful)
    server 10.0.0.2:3000;
    server 10.0.0.3:3000 backup; # Only used others fail
}

server {
    listen 80;
    server_name myweb.com;

    location / {
        proxy_pass http://my_cluster_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
Convert Nginx **Load Balancer** enterprise.

@quiz: Nginx, block `location /static/` other `location ~ \.css$`. Request `/static/style.css`, execute default?
@option: location /static/
@correct: location ~ \.css$ (Regex priority prefix)
@option: None, 404.
@option: First written.

@section: 4. HTTPS and Hardening SSL/TLS

Green lock not enough. HTTPS vulnerable old protocols (SSLv3, TLS 1.0) weak ciphers.

### Strong Diffie-Hellman Params
Default 1024 bits intelligence break. Generate 2048 4096 unique.
```bash
$ sudo openssl dhparam -out /etc/nginx/dhparam.pem 2048
```
*(Coffee time).*

### Config Nginx Armored (Mozilla Modern)
Don't invent. Mozilla standards.

```nginx
ssl_certificate /etc/letsencrypt/live/myweb.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/myweb.com/privkey.pem;

# Protocols: Only TLS 1.2 1.3. Kill SSL TLS 1.0/1.1
ssl_protocols TLSv1.2 TLSv1.3;

# Ciphers (Cipher Suites)
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
ssl_prefer_server_ciphers off;

# HSTS (Strict Transport Security)
# Browser: "1 year, NEVER HTTP this web, force HTTPS".
add_header Strict-Transport-Security "max-age=63072000" always;

# Use strong DHparam
ssl_dhparam /etc/nginx/dhparam.pem;

# OCSP Stapling (Improve verify speed)
ssl_stapling on;
ssl_stapling_verify on;
```

@section: 5. Troubleshooting: Web Down

Server "500 Internal Server Error" "502 Bad Gateway".

**1. Service Status:**
`systemctl status nginx`
Failed? Why.

**2. Syntax:**
Restart Nginx ALWAYS:
`sudo nginx -t`
Syntax error (semicolon), tell here not crash.

**3. Logs (Truth):**
Error 500 (App), log app Apache/Nginx.
`tail -f /var/log/nginx/error.log`
Error 502 (Bad Gateway), Nginx ok, Backend (PHP-FPM, Node.js, Python) down silent. Blame backend.

**4. Permissions:**
Classic Apache/Nginx.
User `www-data` needs **Read** files **Execute** folders.
`ls -l` `root:root 700`, Nginx no read "403 Forbidden".
Fix:
`chown -R www-data:www-data /var/www/html`

@quiz: Configuring server "502 Bad Gateway" browser. Meaning?
@option: Nginx bad config.
@option: SSL expired.
@correct: Nginx working received request, backend service (PHP, Node, Python) down error.
@option: Internet cut.

@section: Module Summary

1.  **Apache** modular, flexible, `.htaccess`, hosting. MPM Event production.
2.  **Nginx** asynchronous, light, static content, reverse proxy, high load (C10K).
3.  **Config:** No defaults. Hide versions, Gzip, KeepAlive.
4.  **Security:** Disable old (SSLv3, TLS1.0). HSTS.
5.  **Diag:** `nginx -t`. Logs errors 5xx.

Knowledge build infrastructure startup.
