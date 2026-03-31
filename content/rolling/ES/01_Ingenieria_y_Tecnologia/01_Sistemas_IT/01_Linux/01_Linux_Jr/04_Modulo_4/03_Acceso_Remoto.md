@title: SSH: acceso remoto seguro y claves públicas
@icon: 🔐
@description: Conectar por SSH, autenticación por clave, ficheros ~/.ssh y buenas prácticas para administrar servidores remotos.
@order: 3

# Acceso remoto con SSH: sesiones seguras y claves

Bienvenido a la herramienta más importante de la historia de Internet.

Si has estado siguiendo el curso, has aprendido a usar la terminal en tu propio ordenador. Eso está muy bien, pero el mundo real no funciona así.
En el mundo profesional, los ordenadores que gestionas (servidores web, bases de datos, supercomputadoras) no están en tu mesa. No tienen monitor. No tienen teclado. Están en un rack en un centro de datos en Virginia, en Frankfurt o en Singapur. Son cajas de metal frías y ruidosas apiladas por miles.

¿Cómo controlas una máquina que está a 5.000 kilómetros de distancia como si estuvieras sentado delante de ella?

La respuesta es **SSH (Secure Shell)**.

SSH no es solo un programa; es un protocolo. Es un tubo mágico, invisible e indestructible que conecta tu teclado con el cerebro de un servidor remoto.
Antes de SSH, usábamos **Telnet**. Telnet enviaba tus contraseñas y comandos en texto plano por los cables de internet. Cualquiera con un sniffer podía leer tu contraseña. Era como enviar tu tarjeta de crédito escrita en una postal.
SSH, nacido en 1995, es como enviar esa postal dentro de un camión blindado, escoltado por tanques, dentro de un túnel subterráneo. Es cifrado de extremo a extremo.

En esta guía masiva, vas a pasar de "saber conectarte" a ser un maestro de la criptografía aplicada. Aprenderás a usar llaves en lugar de contraseñas, a saltar entre servidores, a crear túneles secretos para burlar firewalls y a transferir archivos de forma segura.

Prepárate. Vamos a hackear el planeta (legalmente).

@section: 1. La Primera Conexión: El Saludo de Manos

Para conectarte a otro ordenador, necesitas tres cosas:
1.  **El Cliente:** Tu ordenador (Linux y Mac ya lo tienen. Windows usa PowerShell o PuTTY).
2.  **El Servidor:** La máquina remota debe tener instalado y corriendo el servicio `openssh-server`.
3.  **La Dirección:** La IP o el dominio del servidor.

### Sintaxis Básica
El comando es engañosamente simple:

`ssh [USUARIO]@[SERVIDOR]`

Ejemplo:
```bash
$ ssh juan@192.168.1.50
```
*(Traducción: "Hola, soy juan. Quiero entrar en la máquina 192.168.1.50").*

### El Primer Encuentro (Fingerprint)
La **primera vez** que te conectes a un servidor nuevo, verás un mensaje que asusta a los novatos. Es crucial que entiendas qué significa.

```text
The authenticity of host '192.168.1.50 (192.168.1.50)' can't be established.
ED25519 key fingerprint is SHA256:RO42/uZq...
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

**¿Qué está pasando aquí?**
SSH es paranoico. Quiere evitar un ataque llamado **Man-in-the-Middle (MITM)**.
*   Tú crees que te conectas al servidor de tu banco.
*   Pero un hacker intercepta el cable y te presenta *su* servidor falso.
*   Tú escribes tu contraseña y el hacker se la queda.

Para evitar esto, cada servidor SSH tiene una **Huella Digital (Fingerprint)** única basada en una clave criptográfica.
En este mensaje, tu ordenador te está diciendo:
*"Oye, nunca he hablado con este servidor 192.168.1.50. Él dice que su DNI es `RO42/uZq...`. ¿Te fías de que es quien dice ser?"*

En un entorno de máxima seguridad, deberías llamar al administrador del servidor y preguntarle: *"Oye, ¿cuál es la huella del servidor?"*. Si coincide, escribes `yes`.

### El Archivo `known_hosts`
Cuando escribes `yes`, SSH guarda esa huella en un archivo en tu ordenador: `~/.ssh/known_hosts`.
A partir de ahora, SSH recordará que la IP `192.168.1.50` tiene la huella `RO42...`. No te volverá a preguntar.

**¡ALERTA ROJA! "Remote Host Identification Has Changed"**
Si un día te conectas al mismo servidor y ves una pantalla roja gigante con este aviso:
```text
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
```
Significa que la huella ha cambiado.
*   **Opción A (Inocente):** El administrador reinstaló el sistema operativo del servidor. Al reinstalar, se generan huellas nuevas.
*   **Opción B (Ataque):** Alguien ha interceptado tu conexión y te está redirigiendo a un servidor falso.

Si sabes que es la Opción A, tienes que borrar la huella vieja de tu archivo `known_hosts` para que te deje conectar de nuevo:
```bash
$ ssh-keygen -R 192.168.1.50
```

@section: 2. Autenticación: La Muerte de las Contraseñas

Por defecto, SSH te pide la contraseña del usuario.
Escribir contraseñas es:
1.  **Lento:** Tienes que teclearla cada vez.
2.  **Inseguro:** Las contraseñas se pueden adivinar (fuerza bruta) o robar.

Los profesionales usan **Criptografía Asimétrica (Par de Claves)**.

### La Metáfora del Candado y la Llave
Imagina un sistema de dos piezas:
1.  **Clave Pública (El Candado):** Puedes hacer millones de copias y dárselas a todo el mundo. Sirve para *cerrar* cosas. No sirve para abrir.
2.  **Clave Privada (La Llave Maestra):** Solo tienes una. Nunca se la das a nadie. La guardas bajo tu almohada. Sirve para *abrir* lo que se cerró con tu candado público.

**El proceso de autenticación sin password:**
1.  Tú generas un par (Llave y Candado) en tu PC.
2.  Vas al servidor y colocas tu **Candado (Pública)** en su puerta.
3.  Cuando intentas entrar, el servidor ve el candado y te dice: *"Solo dejaré pasar a quien tenga la llave de este candado"*.
4.  Tu PC usa la **Llave (Privada)** para demostrar matemáticamente que eres el dueño, sin enviar la llave por la red.
5.  ¡Entras!

### Paso 1: Generar tus llaves (`ssh-keygen`)
En tu ordenador (no en el servidor):
```bash
$ ssh-keygen -t ed25519 -C "mi_email@ejemplo.com"
```
*   `-t ed25519`: Es el algoritmo moderno (más rápido y seguro que el viejo RSA).
*   `-C`: Un comentario para identificar la llave (tu email).

Te preguntará dónde guardarla. Pulsa Enter (default).
Te preguntará una **Passphrase** (Frase de paso).
*   *Recomendado:* Pon una contraseña aquí. Esto cifra tu llave privada en tu disco duro. Si alguien te roba el portátil, no podrá usar tu llave para entrar en tus servidores sin esa frase.

Ahora tienes dos archivos en `~/.ssh/`:
*   `id_ed25519`: **TU LLAVE PRIVADA. NO LA COMPARTAS JAMÁS.**
*   `id_ed25519.pub`: Tu Clave Pública (Candado). Esta es la que repartes.

### Paso 2: Instalar el Candado (`ssh-copy-id`)
Ahora tienes que poner tu candado en el servidor. Hay un comando mágico para esto:

```bash
$ ssh-copy-id juan@192.168.1.50
```
Te pedirá tu contraseña (la "vieja" de usuario) una última vez para poder entrar y pegar la clave.
Una vez termine, prueba a entrar:

```bash
$ ssh juan@192.168.1.50
```
¡Boom! Estás dentro sin escribir la contraseña del usuario (quizás te pida la passphrase de la llave si la configuraste, pero eso es local).

@quiz: ¿Cuál es el archivo que NUNCA debes compartir ni enviar por correo electrónico bajo ninguna circunstancia?
@option: id_ed25519.pub
@option: known_hosts
@correct: id_ed25519 (La clave privada)
@option: config

@section: 3. El Archivo `config`: Tu Agenda de Contactos

Conectar así es tedioso: `ssh desarrollador@servidor-aws-produccion-01.miempresa.com -p 2222`.
¿Y si pudieras escribir solo `ssh pro`?

Puedes.
En tu carpeta `~/.ssh/`, crea un archivo llamado `config`.

```bash
$ nano ~/.ssh/config
```

Dentro, puedes definir "alias" para tus servidores:

```text
Host pro
    HostName servidor-aws-produccion-01.miempresa.com
    User desarrollador
    Port 2222
    IdentityFile ~/.ssh/id_trabajo

Host casa
    HostName 192.168.1.50
    User juan

Host *
    User root
```

**Análisis:**
*   Ahora, al escribir `ssh pro`, SSH lee el archivo, ve que "pro" significa conectarse a ese host largo, con ese usuario, a ese puerto y usando esa llave específica.
*   Al escribir `ssh casa`, se conecta a la IP local con usuario juan.
*   `Host *` aplica configuraciones a todos los demás servidores (por defecto usar root si no se dice nada).

Esto te ahorra millones de pulsaciones de teclas al año.

@section: 4. `scp` y `sftp`: Teletransportando Archivos

SSH no es solo para escribir comandos. También sirve para mover datos. Usa el mismo túnel seguro.

### `scp` (Secure Copy)
Funciona exactamente igual que el comando `cp` local, pero a través de la red.

**Estructura:**
`scp [ORIGEN] [DESTINO]`

**Subir un archivo (De mi PC al Servidor):**
```bash
$ scp informe.pdf juan@192.168.1.50:/home/juan/documentos/
```
*(Fíjate en los dos puntos `:` después de la IP. Eso separa la dirección de la ruta).*

**Bajar un archivo (Del Servidor a mi PC):**
```bash
$ scp juan@192.168.1.50:/var/log/syslog .
```
*(El punto final significa "guárdalo aquí, en mi carpeta actual").*

**Copiar carpetas enteras:**
Usa `-r` (Recursivo).
```bash
$ scp -r carpeta_fotos juan@192.168.1.50:/home/juan/
```

### `sftp` (Secure FTP)
Si necesitas navegar, ver qué hay y subir/bajar varias cosas, `scp` es incómodo. Usa `sftp`.
Es una sesión interactiva.

```bash
$ sftp juan@192.168.1.50
sftp> ls        (Lista archivos en el SERVIDOR)
sftp> lls       (Lista archivos en TU PC - Local ls)
sftp> get archivo.txt  (Descarga)
sftp> put foto.jpg     (Sube)
sftp> bye       (Salir)
```
Casi todos los clientes gráficos de FTP (como FileZilla) soportan el protocolo SFTP. Solo tienes que poner el puerto 22.

@section: 5. Túneles SSH: La Magia Negra

Aquí es donde te conviertes en un mago.
Imagina este escenario:
*   Hay un servidor de base de datos (MySQL) en la oficina.
*   Por seguridad, el firewall de la oficina bloquea el puerto 3306 (MySQL) desde internet. Nadie puede entrar.
*   Solo está abierto el puerto 22 (SSH).

¿Cómo te conectas a la base de datos desde tu casa con tu herramienta gráfica favorita?
Respuesta: **Un Túnel SSH (Local Port Forwarding).**

Le dices a SSH: *"Oye, coge el puerto 9000 de MI ordenador, mételo por el túnel SSH, y cuando salga al otro lado, conéctalo al puerto 3306 del servidor"*.

**El Comando:**
```bash
$ ssh -L 9000:localhost:3306 usuario@servidor-oficina
```
*   `-L`: Local forwarding.
*   `9000`: Puerto en tu máquina.
*   `localhost`: El destino *desde la perspectiva del servidor* (el servidor se conecta a sí mismo).
*   `3306`: El puerto destino.

**Resultado:**
Ahora configuras tu cliente MySQL para conectarse a `localhost:9000`.
¡Magia! Tus datos entran en tu puerto 9000, viajan cifrados por SSH, salen en el servidor y entran en la base de datos como si estuvieras allí mismo. Has "burlado" el firewall de forma segura.

@quiz: Estás usando `scp` para descargar un archivo del servidor a tu carpeta actual, pero olvidaste poner el punto `.` al final del comando. ¿Qué pasa?
@option: El archivo se descarga en la carpeta /tmp.
@option: El comando falla y te muestra la ayuda.
@correct: El comando fallará o se comportará de forma inesperada porque le falta el argumento de destino.
@option: Se descarga en tu carpeta Home.

@section: 6. Hardening: Blindando el Servidor

Si pones un servidor SSH en internet (puerto 22), en cuestión de **minutos** empezarás a recibir ataques de bots intentando adivinar contraseñas (`root/123456`, `admin/admin`). Miles al día.

Debes protegerte. La configuración del servidor está en `/etc/ssh/sshd_config`.

### Los 3 Mandamientos de Seguridad SSH

1.  **Prohibir el acceso a Root:**
    Nunca, jamás permitas entrar como root directo. Entra como usuario normal y luego usa `sudo`.
    *   Editar `/etc/ssh/sshd_config`:
    *   `PermitRootLogin no`

2.  **Desactivar Contraseñas:**
    Una vez que tengas tus llaves funcionando, apaga la autenticación por contraseña. Así, aunque un hacker adivine tu clave, no podrá entrar.
    *   `PasswordAuthentication no`

3.  **Cambiar el Puerto (Opcional pero recomendado):**
    Mover SSH del puerto 22 al 2222 reduce el "ruido" de los bots automáticos (aunque un hacker decidido lo encontrará igual).
    *   `Port 2222`

**Aplicar cambios:**
Siempre que toques el archivo de config, debes reiniciar el servicio para que surta efecto:
```bash
$ sudo systemctl restart ssh
```
*¡Cuidado! No cierres tu sesión actual hasta comprobar en OTRA terminal que puedes entrar con la nueva configuración, o te quedarás fuera para siempre.*

### Fail2Ban
Es un programa adicional vital. `sudo apt install fail2ban`.
Vigila los logs. Si ve que una IP ha fallado la contraseña 5 veces en 1 minuto, añade una regla al Firewall y **banea** esa IP durante una hora (o para siempre). Es tu guardaespaldas automático.

@section: 7. Troubleshooting: Cuando SSH Falla

A veces intentas conectar y falla. O pide contraseña cuando no debería.
El comando de diagnóstico es **`-v` (Verbose)**.

```bash
$ ssh -v juan@servidor
```
Te mostrará todo el proceso técnico:
1.  "Connecting to..."
2.  "Server offered these authentication methods..."
3.  "Trying private key..."

Si necesitas más detalle, usa `-vv` o incluso `-vvv` (Extreme Verbose).

**Problema común: "Permissions are too open"**
SSH es muy estricto con los permisos de tus llaves. Si tu llave privada tiene permisos de lectura para "otros" (cualquiera en tu PC puede robarla), SSH se niega a usarla.
*   **Solución:**
    ```bash
    $ chmod 700 ~/.ssh
    $ chmod 600 ~/.ssh/id_ed25519
    ```
    (Solo yo puedo entrar en la carpeta, solo yo puedo leer la llave).

@section: Resumen / Cheat Sheet

| Acción | Comando |
| :--- | :--- |
| **Conectar** | `ssh user@host` |
| **Generar Llaves** | `ssh-keygen -t ed25519 -C "email"` |
| **Enviar Llave** | `ssh-copy-id user@host` |
| **Copiar al server** | `scp archivo user@host:/ruta` |
| **Copiar del server**| `scp user@host:/ruta/archivo .` |
| **SFTP** | `sftp user@host` |
| **Debug** | `ssh -v user@host` |
| **Config Cliente** | `~/.ssh/config` |
| **Config Servidor** | `/etc/ssh/sshd_config` |

SSH es el cordón umbilical del administrador de sistemas. Cuídalo, protégelo y apréndelo bien. Es lo que separa a los usuarios de escritorio de los ingenieros de sistemas.