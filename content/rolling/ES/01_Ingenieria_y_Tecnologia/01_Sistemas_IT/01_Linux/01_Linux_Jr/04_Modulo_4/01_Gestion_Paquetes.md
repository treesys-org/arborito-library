@title: Gestores de paquetes: apt, dnf, repositorios y dependencias
@icon: 🧩
@description: Instalar y actualizar software con apt (Debian/Ubuntu) y dnf/yum (RHEL/Fedora); repositorios, firmas y resolución de dependencias.
@order: 1

# Gestión de paquetes en Linux: apt, dnf y repositorios

Verás cómo instalar y actualizar software en familias **Debian/Ubuntu** (`apt`, `dpkg`) y **RHEL/Fedora** (`dnf`, `rpm`): **repositorios**, confianza (GPG), resolución de **dependencias** y diferencias prácticas que te salvarán en servidor y en certificación.

@section: Mapa LPIC-1 — Módulo 4 (paquetes, red, servicios esenciales, scripting)

Encaje con **LPIC-1 (102 / 109 / 108 / 105)**:

*   **102.3 gestores de paquetes:** `apt`, `dnf`/`yum`, `rpm`, `dpkg`; repositorios; resolución de dependencias; firmas GPG en repos (concepto).
*   **102.4 compilar desde fuente:** nociones enlazadas con dependencias de desarrollo (`build-essential`, `Development Tools`) cuando instales software no empaquetado.
*   **109.1 fundamentos de red:** interfaces, direccionamiento, DNS y diagnóstico (`02_Redes_Basicas.md`).
*   **109.2 servicios persistentes:** `systemctl` a nivel usuario de operador (detalle en avanzado).
*   **108.1 servicios de registro y `journald`:** `04_Logs.md`.
*   **105.1 shell y scripting:** variables, comillas, tests, funciones (`05_Intro_Scripting.md`).
*   **SSH:** acceso remoto (`03_Acceso_Remoto.md`) — pieza clave en **cualquier** certificación práctica.

Bienvenido a la lección que cambiará tu forma de entender la instalación de software.

Si vienes de Windows, tu cerebro tiene un hábito muy arraigado:
1.  Necesitas un programa (ej: Firefox).
2.  Abres el navegador.
3.  Buscas "descargar firefox" en Google.
4.  Entras en una web (esperando que sea la oficial y no una de malware).
5.  Buscas un botón de "Download".
6.  Bajas un archivo `.exe` o `.msi`.
7.  Haces doble clic y pulsas "Siguiente, Siguiente, Siguiente".

**En Linux, esto se considera una práctica bárbara, insegura y obsoleta.**

Linux inventó el concepto de "App Store" décadas antes que el iPhone. En Linux, el software se gestiona de forma **centralizada**.
No buscas el software; le pides a tu sistema que te lo traiga.

En esta guía masiva, vamos a diseccionar el **Gestor de Paquetes**. Entenderás por qué es más seguro, qué son los repositorios, cómo se resuelven las dependencias automáticamente y cómo manejar las herramientas `apt` (Debian/Ubuntu) y `dnf` (RedHat/Fedora) como un profesional.

@section: 1. El Cambio de Paradigma: ¿Qué es un Paquete?

Un **Paquete** es un archivo comprimido (similar a un .zip) que contiene:
1.  **Los Binarios:** El programa ejecutable en sí.
2.  **Configuración:** Archivos por defecto para `/etc`.
3.  **Metadatos:** Información crítica para el gestor:
    *   Nombre y Versión.
    *   Descripción.
    *   **Dependencias:** "Para funcionar necesito la librería X y la librería Y".

### El Problema de las Dependencias (Dependency Hell)
En los viejos tiempos (y en Windows a veces), si instalabas un programa, este podía fallar porque le faltaba una librería `.dll` o `.so`. Tenías que buscar esa librería manualmente, instalarla, y rezar para que fuera la versión correcta.

El **Gestor de Paquetes** soluciona esto matemáticamente.
Si le dices: *"Instala VLC Player"*
El gestor piensa: *"Vale, VLC necesita `libvideo`, `libsound` y `qt-interface`. `qt-interface` necesita `libgraphics`. Voy a descargar e instalar las 5 cosas en el orden correcto automáticamente"*.

### Repositorios: La Fuente de la Verdad
En lugar de descargar cosas de cualquier web, tu sistema Linux tiene una lista de **Repositorios** confiables. Son servidores mantenidos por los creadores de tu distribución (Canonical, Red Hat, Comunidad) que contienen miles de paquetes verificados y firmados digitalmente.

Si un paquete no está en tus repositorios, el sistema no lo "ve".

@section: 2. APT: El Gigante de Debian y Ubuntu

Si usas Ubuntu, Linux Mint, Debian, Kali Linux o Pop!_OS, tu herramienta es **APT** (Advanced Package Tool).
Los paquetes tienen extensión **`.deb`**.

### 2.1 La Lista Sagrada: `sources.list`
Antes de instalar nada, tu ordenador necesita saber dónde buscar. Esa lista de servidores está en un archivo de texto.

```bash
$ cat /etc/apt/sources.list
```
Verás líneas como:
`deb http://archive.ubuntu.com/ubuntu/ jammy main restricted`

*   **deb:** Es un repositorio de binarios (programas listos para usar).
*   **url:** El servidor web donde están los archivos.
*   **jammy:** El nombre en clave de tu versión de Ubuntu (22.04).
*   **main/restricted:** Las secciones (Software libre, drivers propietarios, etc.).

**Importante:** Cuando añades un repositorio nuevo (PPA), se añade una línea a este archivo o se crea un archivo nuevo en `/etc/apt/sources.list.d/`.

### 2.2 Actualizando el Catálogo (`apt update`)
Este es el error número 1 de los novatos.
El comando `sudo apt update` **NO ACTUALIZA TU SOFTWARE**.

Repito: `apt update` **NO** instala nada nuevo.

Lo que hace es:
1.  Descarga la lista de paquetes más reciente desde los servidores.
2.  Compara esa lista con la que tiene tu ordenador.
3.  Toma nota de qué paquetes tienen una versión nueva disponible.

Es como bajar el catálogo de precios del supermercado antes de ir a comprar. Si no haces esto, tu ordenador intentará bajar versiones viejas que ya no existen en el servidor y te dará un "Error 404".

**Regla:** Ejecuta `sudo apt update` siempre antes de instalar algo.

### 2.3 Actualizando el Sistema (`apt upgrade`)
Este es el comando que **SÍ** instala las actualizaciones.

```bash
$ sudo apt upgrade
```
*   Mira la lista de actualizaciones pendientes (gracias al `update` anterior).
*   Te dice: "Se descargarán 500MB. ¿Desea continuar?".
*   Descarga e instala las versiones nuevas de tus programas y del Kernel.

### 2.4 Instalando Software (`apt install`)
Para instalar algo nuevo:

```bash
$ sudo apt install firefox
```
Puedes instalar varios a la vez:
```bash
$ sudo apt install vlc gimp htop neofetch
```

Si el paquete ya está instalado, `apt` intentará actualizarlo a la última versión disponible. Si ya está en la última, te dirá que no hay nada que hacer.

### 2.5 Buscando Paquetes (`apt search` y `apt show`)
No sabes el nombre exacto del paquete. ¿Es `python` o `python3`?

```bash
$ apt search python3
```
Esto te dará una lista enorme.

Para ver detalles de un paquete específico antes de instalarlo:
```bash
$ apt show python3
```
Te dirá la versión exacta, el tamaño, la descripción y de qué depende.

### 2.6 Eliminando Software (`remove` vs `purge`)
Hay dos formas de borrar.

1.  **`remove`:** Desinstala el programa, pero **DEJA** los archivos de configuración en el sistema (en `/etc`).
    *   *Uso:* Si crees que podrías volver a instalarlo en el futuro y no quieres perder tu configuración personalizada.
    ```bash
    $ sudo apt remove nginx
    ```

2.  **`purge`:** Desinstala el programa y **BORRA** todos sus archivos de configuración globales.
    *   *Uso:* Quieres eliminarlo por completo, como si nunca hubiera existido.
    ```bash
    $ sudo apt purge nginx
    ```
    *(Nota: Esto no suele borrar las carpetas ocultas en tu `/home/usuario`, solo las del sistema).*

### 2.7 Limpiando la Basura (`autoremove`)
Cuando instalas `vlc`, se instalan 20 librerías de las que depende.
Si desinstalas `vlc`, esas 20 librerías se quedan en tu sistema "huérfanas". Nadie las necesita, pero ocupan espacio.

APT es listo y te avisará: *"The following packages were automatically installed and are no longer required..."*.

Para limpiar todo eso:
```bash
$ sudo apt autoremove
```
Es muy satisfactorio ver cómo se liberan cientos de megas.

@quiz: ¿Cuál es la diferencia crítica entre `apt update` y `apt upgrade`?
@option: `update` actualiza el kernel y `upgrade` las aplicaciones.
@correct: `update` actualiza la lista de paquetes disponibles (el catálogo), `upgrade` instala las versiones nuevas de los programas.
@option: Son lo mismo, `update` es el nombre antiguo.
@option: `upgrade` es solo para usuarios premium.

@section: 3. DNF: El Poder de Red Hat

Si usas Fedora, Red Hat (RHEL), CentOS o AlmaLinux, tu gestor es **DNF** (Dandified YUM).
Los paquetes tienen extensión **`.rpm`**.

DNF es más moderno que APT en algunos aspectos (gestiona mejor las dependencias complejas y tiene un historial de transacciones mejor), pero los comandos son casi idénticos.

| Acción | APT (Debian/Ubuntu) | DNF (Fedora/RHEL) |
| :--- | :--- | :--- |
| Refrescar lista | `apt update` | `dnf check-update` |
| Actualizar todo | `apt upgrade` | `dnf upgrade` |
| Instalar | `apt install pkg` | `dnf install pkg` |
| Borrar | `apt remove pkg` | `dnf remove pkg` |
| Buscar | `apt search pkg` | `dnf search pkg` |
| Info | `apt show pkg` | `dnf info pkg` |

**Característica única de DNF: Historial**
DNF guarda un registro de todo lo que has hecho. Si instalas algo y rompes el sistema, puedes deshacerlo.

```bash
$ sudo dnf history
$ sudo dnf history undo 5
```
*(Esto deshace la transacción número 5. Una maravilla).*

@section: 4. Paquetes Universales: Snap, Flatpak y AppImage

El problema de APT y DNF es la **fragmentación**.
Si creas un programa, tienes que empaquetarlo `.deb` para Ubuntu, `.rpm` para Fedora, y otro para Arch. Es mucho trabajo. Además, las versiones de las librerías pueden entrar en conflicto.

Para solucionar esto, nacieron los **Paquetes Universales**. Funcionan en CUALQUIER distribución Linux.

### 4.1 Flatpak (El Favorito de la Comunidad)
*   **Filosofía:** Descentralizado.
*   **Cómo funciona:** Las apps corren en un entorno aislado (Sandbox). Traen sus propias librerías, no usan las del sistema.
*   **Tienda:** Flathub.org.
*   **Pros:** Muy seguro, siempre tienes la última versión de la app independientemente de tu versión de Linux.
*   **Contras:** Ocupan más espacio en disco (porque duplican librerías).

```bash
$ flatpak install flathub org.gimp.GIMP
$ flatpak run org.gimp.GIMP
```

### 4.2 Snap (El Invento de Canonical/Ubuntu)
*   **Filosofía:** Centralizado (controlado por Canonical).
*   **Cómo funciona:** Similar a Flatpak, pero también permite empaquetar servicios de servidor y kernel, no solo apps de escritorio.
*   **Pros:** Muy fácil de usar en Ubuntu. Actualizaciones automáticas invisibles.
*   **Contras:** El servidor es propietario (cerrado). El arranque de las apps puede ser más lento. Genera muchos dispositivos "loop" virtuales al hacer `lsblk`.

```bash
$ sudo snap install spotify
```

### 4.3 AppImage (El Portátil)
*   **Filosofía:** "Un archivo, una app".
*   **Cómo funciona:** Te bajas un archivo `.AppImage`. Le das permiso de ejecución (`chmod +x`). Lo ejecutas.
*   **Pros:** No se instala nada. Es como los `.exe` portables de Windows. Ideal para probar software sin ensuciar el sistema.
*   **Contras:** No se actualizan solos. Tienes que bajar el nuevo archivo manualmente.

@quiz: ¿Cuál es la principal ventaja de usar Flatpak o Snap frente a los paquetes nativos (.deb/.rpm)?
@option: Son más ligeros y rápidos.
@option: Se integran mejor con el kernel.
@correct: Funcionan en cualquier distribución Linux y traen sus propias dependencias, evitando conflictos.
@option: No requieren conexión a internet.

@section: 5. La Instalación Manual: `dpkg` y `rpm`

A veces, un programa no está en los repositorios, pero el desarrollador te ofrece un archivo `.deb` para descargar (ej: Google Chrome, VS Code, Discord).

### Instalando un `.deb` suelto
No puedes usar `apt install archivo.deb` directamente (en versiones antiguas). Necesitas la herramienta de bajo nivel: **`dpkg`**.

```bash
# Descargar
$ wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

# Instalar (dará error de dependencias casi seguro)
$ sudo dpkg -i google-chrome-stable_current_amd64.deb

# Arreglar las dependencias que faltan
$ sudo apt install -f
```
El comando `apt install -f` (fix-broken) es mágico. Ve que intentaste instalar Chrome con `dpkg` pero falló porque faltaban librerías, y las descarga e instala para completar el proceso.

**Método Moderno:**
En las versiones recientes de `apt`, puedes darle la ruta del archivo y él se encarga de todo (usa `dpkg` por debajo pero resuelve dependencias):
```bash
$ sudo apt install ./google-chrome-stable_current_amd64.deb
```
*(El `./` es obligatorio para que sepa que es un archivo local y no un paquete del repositorio).*

@section: 6. Compilar desde el Código Fuente (The Hard Way)

Esto es lo que hacíamos en los 90. Hoy en día es raro tener que hacerlo a menos que necesites un software muy específico o una versión que salió ayer.

El proceso clásico es la "Trinidad":
1.  **Bajar el código fuente:** (`git clone ...` o bajar un `.tar.gz`).
2.  **`./configure`:** Un script que revisa tu sistema para ver si tienes todas las herramientas y librerías necesarias para compilar. Si te falta algo, fallará aquí.
3.  **`make`:** Compila el código. Convierte el texto humano (C/C++) en binario máquina. Esto pone la CPU al 100% y tarda tiempo.
4.  **`sudo make install`:** Copia los binarios resultantes a las carpetas del sistema (`/usr/local/bin`).

**Por qué deberías evitarlo si eres novato:**
*   El gestor de paquetes **NO sabe** que has instalado esto.
*   No se actualizará solo.
*   Es difícil de desinstalar (a menos que guardes la carpeta de código y hagas `sudo make uninstall`).
*   Ensucia el sistema.

@section: 7. Solución de Problemas Comunes

### Error: "Could not get lock /var/lib/dpkg/lock"
Estás intentando instalar algo y ves esto:
`E: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 1234 (apt)`

**Causa:** Solo puede haber **UN** proceso `apt` ejecutándose a la vez en todo el sistema.
Si ves esto, significa que:
1.  Tienes otra terminal abierta instalando algo.
2.  Una actualización automática está corriendo en segundo plano.
3.  Cerraste una terminal a lo bruto en mitad de una instalación y el proceso se quedó "zombie" bloqueando el archivo.

**Solución:**
1.  Espera. A veces es solo la actualización automática.
2.  Si no termina, mata el proceso: `sudo kill 1234` (el PID que te dice el error).
3.  Si sigue fallando (muy raro), borra el archivo de bloqueo: `sudo rm /var/lib/dpkg/lock-frontend` y luego `sudo dpkg --configure -a` para reparar la base de datos.

### Error: "404 Not Found" al hacer update
Estás usando una versión de Ubuntu o Fedora que es tan vieja que ha llegado al "End of Life" (EOL) y han movido los repositorios al archivo histórico, o estás intentando acceder a un PPA (repositorio personal) que ya no existe.
**Solución:** Desactiva ese repositorio en `/etc/apt/sources.list` o actualiza tu distribución completa a una versión soportada.

### Error: "Held Broken Packages"
Has instalado cosas mezclando repositorios y ahora tienes un lío de versiones (A necesita B v1.0, pero C necesita B v2.0).
**Solución:** `sudo apt install -f` suele arreglarlo. Si no, usa `aptitude` (una interfaz más lista que apt) que te propondrá varias soluciones matemáticas para resolver el conflicto (borrar A, actualizar C, etc.).

@section: 8. Laboratorio Práctico: Gestión de Software

Vamos a practicar el ciclo de vida del software.

1.  **Actualizar el catálogo:**
    ```bash
    $ sudo apt update
    ```

2.  **Instalar un programa divertido (`cmatrix`):**
    ```bash
    $ sudo apt install cmatrix
    ```

3.  **Ejecutarlo:**
    ```bash
    $ cmatrix
    ```
    *(Siéntete como Neo en Matrix. Pulsa `q` o `Ctrl+C` para salir).*

4.  **Verificar la instalación:**
    ```bash
    $ which cmatrix
    $ dpkg -L cmatrix  # (Muestra todos los archivos que ha creado)
    ```

5.  **Desinstalarlo:**
    ```bash
    $ sudo apt remove cmatrix
    ```

6.  **Limpiar:**
    ```bash
    $ sudo apt autoremove
    ```

7.  **Instalar un .deb externo (Opcional):**
    Descarga "Visual Studio Code" o "Google Chrome" desde su web y prueba a instalarlo con `sudo apt install ./archivo.deb`.

@section: Resumen / Cheat Sheet (APT)

| Acción | Comando | Explicación |
| :--- | :--- | :--- |
| **Refrescar** | `sudo apt update` | Baja la lista de versiones nuevas. NO instala. |
| **Actualizar** | `sudo apt upgrade` | Instala las versiones nuevas. |
| **Instalar** | `sudo apt install [paquete]` | Instala un programa. |
| **Reinstalar** | `sudo apt reinstall [paquete]` | Útil si has borrado un archivo por error. |
| **Borrar** | `sudo apt remove [paquete]` | Borra programa, deja configuración. |
| **Purgar** | `sudo apt purge [paquete]` | Borra programa Y configuración. |
| **Limpiar** | `sudo apt autoremove` | Borra dependencias huérfanas. |
| **Buscar** | `apt search [texto]` | Busca en la descripción de paquetes. |
| **Info** | `apt show [paquete]` | Muestra detalles técnicos. |
| **Arreglar** | `sudo apt install -f` | Intenta arreglar una instalación rota. |

¡Felicidades! Ahora sabes gestionar el software de tu sistema de forma segura, limpia y profesional. Nunca más descargarás un `.exe` de una web dudosa.