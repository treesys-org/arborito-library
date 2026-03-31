@title: Navegación en terminal: pwd, cd, ls y rutas
@icon: 🗺️
@description: Moverte por el sistema de archivos en CLI; rutas absolutas y relativas; contexto FHS para LPIC-1.
@order: 1

# Navegación en terminal: pwd, cd, ls y el sistema de archivos

Aquí aprenderás a orientarte en la **shell**: saber en qué carpeta estás (`pwd`), moverte (`cd`) y listar contenido (`ls`), usando **rutas absolutas y relativas** y el mapa mental del **FHS** (jerarquía de directorios) que exige LPIC-1.

@section: Mapa LPIC-1 — Módulo 2 (comandos GNU, FHS, ayuda)

Alineación con dominios típicos **LPIC-1 (103 / 104)** y práctica de certificación:

*   **103.1 Trabajar en la línea de órdenes:** `pwd`, `cd`, rutas absolutas/relativas, historial, comillas, expansiones.
*   **103.2 Procesar flujos de texto:** redirecciones y tuberías (se completan en `03_Flujos_Datos.md`); filtros en `04_Busqueda_Filtros.md`.
*   **103.3 Administración básica de archivos:** `ls`, `cp`, `mv`, `rm`, enlaces (en lecciones de gestión de archivos del módulo).
*   **104.1 FHS:** qué va en `/bin`, `/etc`, `/var`, `/home` — integrado en la navegación y rutas.
*   **104.6 enlaces simbólicos y duros:** en `02_Gestion_Archivos.md`.
*   **RHEL vs Debian:** mismos comandos GNU; cambian rutas de configuración por paquetes (`/etc/os-release` para identificar familia).

**FHS en profundidad:** el “mapa de la mansión” completo — `/boot`, `/proc`, `/sys`, `/run`, `/usr/local`, fusión `/usr`, diferencia entre `/tmp` y `/var/tmp`, tabla LPIC y laboratorio — está desarrollado como capítulo de manual en la lección **Instalación y almacenamiento en Linux** (Módulo 1, `03_Instalacion.md`, sección 2). En este módulo usas esas rutas a diario con `cd` y `ls`; allí entiendes **por qué** existen.

Bienvenido al Módulo 2. Si estás aquí, ya has superado la instalación y entiendes qué es Linux. Pero ahora te enfrentas a la famosa "Pantalla Negra" (la Terminal).

Para un usuario nuevo que viene de Windows o macOS, la terminal puede parecer una cueva oscura.
*   En el entorno gráfico (GUI), si quieres abrir una carpeta, la **ves** y haces doble clic.
*   En la terminal (CLI), no ves nada hasta que lo pides. Estás "ciego" por defecto.

**No entres en pánico.**

Navegar por la terminal no es difícil; es simplemente diferente. De hecho, una vez que aprendes, te darás cuenta de que usar el ratón para buscar un archivo en una jerarquía de 10 carpetas es increíblemente lento y doloroso. La terminal es teletransportación.

En esta lección masiva, vamos a convertirte en un experto en movilidad. Vamos a romper cada comando pieza por pieza y explicarte no solo *cómo* funcionan, sino *por qué* existen y los trucos que usan los profesionales para no escribir tanto.

@section: 1. El Concepto Mental: El Árbol Invertido

Antes de escribir una sola letra, necesitas visualizar dónde estás.

El sistema de archivos de Linux es un **Árbol Invertido**.
*   Las raíces están arriba del todo.
*   Las ramas (carpetas) cuelgan hacia abajo.
*   Las hojas (archivos) están al final de las ramas.

En Windows, estás acostumbrado a tener varios árboles:
*   Árbol C: (El sistema)
*   Árbol D: (Tus datos)
*   Árbol E: (Tu USB)

En Linux, **solo hay un árbol gigante**. Todo empieza en un punto único llamado **La Raíz (The Root)**, representado por una simple barra inclinada: **`/`**.

No importa cuántos discos duros tengas, todos son ramas injertadas en este único árbol magno. Tu objetivo en esta lección es aprender a trepar por estas ramas con agilidad.

@section: 2. `pwd`: El "Usted Está Aquí"

Imagina que te despiertas en una habitación desconocida dentro de un castillo gigante. Está oscuro. No sabes si estás en el sótano, en la torre o en la cocina.
¿Cuál es la primera pregunta que te haces?
*"¿Dónde estoy?"*

En Linux, esa pregunta se hace con el comando `pwd`.

### Significado
`pwd` son las siglas de **Print Working Directory** (Imprimir Directorio de Trabajo).
*   **Print:** En informática antigua, "imprimir" significaba mostrar texto en la pantalla (no necesariamente en papel).
*   **Working Directory:** Es la carpeta en la que estás "de pie" ahora mismo.

### Probémoslo
Abre tu terminal y escribe:

```bash
$ pwd
```

La respuesta será algo como:
`/home/tu_usuario`

### Anatomía de una Ruta (Path)
Esa respuesta `/home/tu_usuario` es una **Ruta**. Vamos a diseccionarla:
1.  La primera `/`: Representa **La Raíz**. Es el inicio del universo.
2.  `home`: Es una carpeta dentro de la raíz.
3.  La segunda `/`: Es solo un separador. Significa "dentro de".
4.  `tu_usuario`: Es una carpeta dentro de `home`.

Así que `/home/tu_usuario` se lee: *"Empieza en la Raíz, entra en la carpeta home, y luego entra en la carpeta tu_usuario"*.

**¿Por qué es importante?**
Porque cualquier comando que ejecutes (crear un archivo, borrar una foto) ocurrirá **AQUÍ**, en tu directorio de trabajo. Si no sabes dónde estás, puedes borrar accidentalmente cosas que no querías.
**Regla de oro:** Si te sientes perdido, escribe `pwd`.

@section: 3. `ls`: Los Ojos del Sistema

Ahora sabes dónde estás, pero la habitación está a oscuras. Necesitas encender la luz para ver qué hay a tu alrededor.
Ese interruptor de luz es el comando `ls`.

### Significado
`ls` viene de **List** (Listar). Su trabajo es enumerar los archivos y carpetas que hay en tu ubicación actual.

### Nivel 1: `ls` Básico
Escribe simplemente:

```bash
$ ls
```

Verás algo así:
`Documentos  Descargas  Imágenes  Música  Vídeos  nota.txt`

**El Código de Colores:**
La mayoría de las terminales modernas te ayudan con colores (aunque esto puede variar según la configuración):
*   **Azul:** Directorios (Carpetas).
*   **Blanco (o negro):** Archivos de texto o normales.
*   **Verde:** Archivos ejecutables (Programas o scripts).
*   **Cian:** Enlaces simbólicos (Accesos directos).
*   **Rojo:** Archivos comprimidos (.zip, .tar.gz).

### Nivel 2: `ls -a` (La Visión de Rayos X)
En Linux, hay archivos que son tímidos. Se llaman **Archivos Ocultos**.
Cualquier archivo o carpeta cuyo nombre empiece por un punto (`.`) es oculto. El sistema los usa para guardar configuraciones sin molestarte.

Si haces un `ls` normal, no los ves. Para verlos, necesitas la opción (flag) `-a` (**All** / Todo).

```bash
$ ls -a
.  ..  .bashrc  .profile  .config  Documentos  Descargas
```

¡Vaya! Han aparecido un montón de cosas nuevas.
*   `.bashrc`: Es el archivo de configuración de tu terminal.
*   `.config`: Donde tus aplicaciones guardan sus preferencias.
*   `.` y `..`: Explicaremos estos dos misteriosos símbolos más adelante, pero son vitales.

### Nivel 3: `ls -l` (El Informe Forense)
A veces, saber el nombre del archivo no es suficiente. Quieres saber:
*   ¿Cuánto ocupa?
*   ¿Cuándo se creó?
*   ¿Quién es el dueño?
*   ¿Tengo permiso para abrirlo?

Para eso usamos la opción `-l` (**Long Format** / Formato Largo).

```bash
$ ls -l
total 64
drwxr-xr-x 2 juan juan 4096 oct 25 10:00 Documentos
-rw-r--r-- 1 juan juan  520 oct 26 14:30 nota.txt
```

Esto parece Matrix, pero vamos a leerlo juntos. Tomemos la línea de `nota.txt`:

1.  `-rw-r--r--`: **Permisos**.
    *   El primer carácter nos dice qué es. Si es un guion `-`, es un archivo. Si es una `d`, es un directorio.
    *   El resto (`rw-r--r--`) dice quién puede leer y escribir (lo veremos en el Módulo 3).
2.  `1`: Número de enlaces (ignóralo por ahora).
3.  `juan` (primero): El **Usuario** dueño del archivo.
4.  `juan` (segundo): El **Grupo** dueño del archivo.
5.  `520`: El **Tamaño** en bytes.
6.  `oct 26 14:30`: Fecha y hora de la última modificación.
7.  `nota.txt`: El nombre.

### Nivel 4: `ls -h` (Humanizando los datos)
¿Sabes cuánto son 10485760 bytes? Yo tampoco.
Los informáticos odian calcular mentalmente. Por eso existe la opción `-h` (**Human Readable** / Legible por Humanos).
Esta opción convierte esos números feos en `K` (Kilobytes), `M` (Megabytes) o `G` (Gigabytes).

Pero `-h` por sí solo no sirve de mucho porque `ls` normal no muestra tamaños. Tienes que combinarlo con `-l`.

**El Combo Definitivo:**
En Linux, puedes juntar las letras de las opciones. En lugar de escribir `ls -l -a -h`, puedes escribir:

```bash
$ ls -lah
```

Este es el comando que usarás el 90% de las veces. Te muestra **todo** (ocultos incluidos), en formato **detallado**, y con tamaños **legibles**.

### Nivel 5: Ordenando el Caos
Cuando tienes 500 archivos, una lista alfabética no siempre es lo mejor.
*   `ls -lt`: Ordenar por **Tiempo** (Time). Los más nuevos primero. Útil para ver qué acabas de descargar.
*   `ls -lS`: Ordenar por **Tamaño** (Size). Los más grandes primero. Útil para limpiar disco.
*   `ls -lr`: Ordenar **Inversamente** (Reverse). Ponlo al final de cualquier otro comando (ej. `ls -ltr`) para darle la vuelta a la lista.

@quiz: Acabas de descargar un archivo pero no recuerdas el nombre. ¿Qué comando te mostraría los archivos de la carpeta actual ordenados por fecha, poniendo los más recientes arriba?
@option: ls -lS
@correct: ls -lt
@option: ls -la
@option: pwd

@section: 4. `cd`: El Teletransportador

Ya sabes dónde estás (`pwd`) y qué hay a tu alrededor (`ls`). Ahora quieres ir a otro sitio.
Usamos `cd`, que significa **Change Directory** (Cambiar Directorio).

El comando `cd` es tu nave espacial. Pero para volar, necesitas darle coordenadas. Estas coordenadas se llaman **Rutas (Paths)**.

Hay dos formas de dar coordenadas: **Absolutas** y **Relativas**. Entender la diferencia es lo que separa a un novato de un usuario intermedio.

### A. Rutas Absolutas (El GPS)
Una ruta absoluta es la dirección completa y exacta desde el origen del universo (la Raíz `/`).
Es como darle a alguien una dirección postal completa: *"Calle Falsa 123, Ciudad, País, Planeta Tierra"*.
No importa dónde estés en el mundo; esa dirección siempre apunta al mismo buzón.

Las rutas absolutas **SIEMPRE empiezan con `/`**.

Ejemplos:
*   `cd /home/juan/Descargas`
*   `cd /var/log`
*   `cd /etc`

**Ventaja:** Nunca fallan. Siempre sabes a dónde vas.
**Desventaja:** Son largas de escribir.

### B. Rutas Relativas (Las Indicaciones Locales)
Una ruta relativa depende de dónde estás ahora mismo.
Es como decir: *"Ve recto, gira en la segunda a la izquierda y entra en la puerta azul"*.
Si estás en Madrid, esas instrucciones te llevan a una panadería. Si estás en Tokio, te llevan a un río.

Las rutas relativas **NUNCA empiezan con `/`**. Empiezan con el nombre de la carpeta a la que quieres entrar.

**Ejemplo Práctico:**
Imagina que estás en `/home/juan` y quieres entrar en `Documentos`.

*   Método Absoluto: `cd /home/juan/Documentos` (Funciona, pero escribes mucho).
*   Método Relativo: `cd Documentos` (El sistema asume que buscas dentro de tu carpeta actual).

### Los Símbolos Mágicos: `.` y `..`
¿Recuerdas cuando hicimos `ls -a` y vimos dos carpetas extrañas llamadas `.` y `..`?
No son carpetas reales. Son atajos del sistema de archivos.

1.  **El Punto `.` (Aquí Mismo):**
    El punto simple representa el directorio actual.
    Parece inútil ahora (`cd .` no hace nada, te quedas donde estás), pero será vital cuando ejecutemos scripts (ej: `./script.sh` significa "ejecuta el script que está *aquí mismo*").

2.  **El Doble Punto `..` (El Padre):**
    Esto es lo más útil que aprenderás hoy. Los dos puntos representan **el directorio de arriba** (el directorio "padre" que contiene al actual).
    Es la forma de "volver atrás" o "salir de la carpeta".

    **Ejercicios de `..`:**
    *   Estás en `/home/juan/Documentos`.
    *   Escribes: `cd ..`
    *   Ahora estás en `/home/juan`.

    Puedes encadenarlos:
    *   Estás en `/home/juan/Documentos`.
    *   Escribes: `cd ../..` (Sube uno, y luego sube otro).
    *   Ahora estás en `/home`.

### C. Atajos de `cd` que te salvan la vida
Los administradores de Linux somos vagos. Odiamos escribir. Aquí tienes los trucos para moverte a la velocidad de la luz:

1.  **Ir a Casa (`cd`):**
    Si escribes `cd` y pulsas Enter (sin nada más), el sistema te teletransporta instantáneamente a tu carpeta personal (`/home/tu_usuario`). Es el botón de "Home" de los videojuegos. Úsalo si te pierdes en las profundidades del sistema.

2.  **La Virgulilla (`~`):**
    El símbolo `~` (se hace con Alt Gr + 4 en teclados españoles) es un sinónimo de "Mi Carpeta Personal".
    *   `cd ~/Fotos` es lo mismo que `cd /home/juan/Fotos`.
    *   Es genial porque funciona desde cualquier lugar.

3.  **El Botón "Last Channel" (`cd -`):**
    ¿Sabes ese botón del mando de la TV que te devuelve al canal que estabas viendo antes?
    `cd -` hace exactamente eso. Te lleva al último directorio en el que estuviste.
    *   Estás en `/etc/nginx`.
    *   Haces `cd /var/www/html`.
    *   Se te olvidó algo en la carpeta anterior. Haces `cd -`.
    *   ¡Boom! Estás de vuelta en `/etc/nginx`.

@quiz: Estás en la carpeta `/var/log` y quieres ir a la carpeta `/var`. ¿Cuál es la forma más rápida (menos caracteres) de hacerlo usando una ruta relativa?
@option: cd /var
@correct: cd ..
@option: cd .
@option: cd ~

@section: 5. La Tecla Más Importante: TAB (Autocompletado)

Si te llevas una sola cosa de esta lección, que sea esto:
**NUNCA ESCRIBAS UN NOMBRE DE ARCHIVO COMPLETO.**

La terminal es inteligente. Si quieres entrar en la carpeta `Documentos_Importantes_2025_Final`, no tienes que escribirlo todo.
Solo escribe: `cd Doc` y pulsa la tecla **TAB** (Tabulador, la que está a la izquierda de la Q).

*   **Si es la única coincidencia:** El sistema rellenará el resto del nombre instantáneamente y pondrá una `/` al final.
*   **Si hay varias coincidencias** (ej: `Documentos` y `Descargas`): El sistema pitará o no hará nada. Pulsa TAB **dos veces** rápidamente. El sistema te mostrará una lista de las posibles opciones. Escribe una letra más para desempatar (ej: `cd Do`) y pulsa TAB otra vez.

Acostúmbrate a pulsar TAB constantemente. No solo ahorra tiempo, sino que **evita errores ortográficos**. Si pulsas TAB y no se completa, sabes inmediatamente que has escrito mal el principio o que el archivo no existe.

@section: 6. Sensibilidad a Mayúsculas (Case Sensitivity)

Aquí es donde los usuarios de Windows sufren.
En Windows, un archivo llamado `Foto.jpg` y `foto.jpg` son el mismo archivo. No puedes tener los dos en la misma carpeta. Windows ignora las mayúsculas.

**En Linux, las mayúsculas importan.**
*   `Archivo.txt`
*   `archivo.txt`
*   `ARCHIVO.TXT`

Son **tres archivos completamente diferentes**. Pueden coexistir en la misma carpeta sin problemas.
Cuando escribas comandos, ten mucho cuidado. `cd documentos` fallará si la carpeta se llama `Documentos` (con D mayúscula).
Por defecto, las carpetas de usuario en Linux (`Documentos`, `Descargas`, `Escritorio`) empiezan con mayúscula.

@section: 7. El Enemigo: Los Espacios en Blanco

En Linux, el espacio (barra espaciadora) es un carácter sagrado. Significa **"aquí termina un comando y empieza el siguiente argumento"**.

Imagina que tienes una carpeta llamada `Mis Fotos`.
Si escribes: `cd Mis Fotos`
El sistema pensará:
1.  Comando: `cd`
2.  Argumento 1: `Mis` (intentará ir a la carpeta "Mis")
3.  Argumento 2: `Fotos` (no sabrá qué hacer con esto)

Te dará un error: `bash: cd: Mis: No such file or directory`.

### Cómo lidiar con espacios
Tienes dos opciones para decirle a la terminal que el espacio es parte del nombre:

1.  **Comillas:** Envuélvelo todo.
    `cd "Mis Fotos"`
    Esta es la forma más fácil de entender para humanos.

2.  **Escape (Barra Invertida `\`):**
    Pon una barra `\` justo antes del espacio. Esto le dice a la terminal: "El siguiente carácter no es especial, es solo texto".
    `cd Mis\ Fotos`

**Truco:** ¡Usa el **TAB**! Si escribes `cd Mis` y pulsas TAB, la terminal es lo suficientemente lista para autocompletar usando las barras invertidas automáticamente (`Mis\ Fotos/`).

@section: 8. Taller Práctico: Tu Primera Excursión

Vamos a poner todo esto en práctica. Abre tu terminal y sigue estos pasos. Intenta predecir qué pasará antes de hacerlo.

1.  **Orientación:**
    *   Comando: `pwd`
    *   *Deberías ver `/home/tu_usuario`.*

2.  **Exploración:**
    *   Comando: `ls -lah`
    *   *Mira tus archivos ocultos y tamaños.*

3.  **Creación de un laberinto:**
    *   Comando: `mkdir -p prueba/nivel1/nivel2`
    *   *(Esto crea una carpeta dentro de otra carpeta dentro de otra).*

4.  **Entrando en la madriguera:**
    *   Comando: `cd prueba/nivel1/nivel2`
    *   Comando: `pwd`
    *   *Verás la ruta larga.*

5.  **Volviendo atrás paso a paso:**
    *   Comando: `cd ..`
    *   Comando: `pwd`
    *   *Ahora estás en `nivel1`.*

6.  **Volviendo a casa rápido:**
    *   Comando: `cd`
    *   Comando: `pwd`
    *   *Estás de vuelta en `/home/tu_usuario`.*

7.  **El truco del "Last Channel":**
    *   Comando: `cd -`
    *   *¡BAM! De vuelta a `prueba/nivel1`.*

8.  **Limpieza:**
    *   Comando: `cd` (volvemos a casa para no borrar la alfombra bajo nuestros pies).
    *   Comando: `rm -r prueba`
    *   *(Borramos el experimento).*

@section: 9. Solución de Problemas Comunes

**Error: "No such file or directory"**
*   *Causa:* Has escrito mal el nombre o la ruta.
*   *Solución:* ¿Usaste mayúsculas correctamente? ¿Usaste TAB para autocompletar? ¿Estás en la carpeta correcta (haz `ls`)?

**Error: "Permission denied"**
*   *Causa:* Estás intentando entrar en una carpeta que no es tuya (por ejemplo, `/root` o la carpeta de otro usuario).
*   *Solución:* Si realmente necesitas entrar y tienes derechos administrativos, usa `sudo ls /root`. (Pero cuidado, no puedes hacer `sudo cd`, porque `cd` no es un programa, es una función de la shell. Para ser root, usa `sudo -i`).

**Error: "Not a directory"**
*   *Causa:* Intentaste hacer `cd` a un archivo de texto (ej: `cd nota.txt`).
*   *Solución:* `cd` es solo para carpetas (directorios). Si quieres ver el archivo, usa `cat`, `less` o `nano`.

@section: Resumen / Cheat Sheet

Guarda esta tabla mentalmente:

| Comando | Acción | Analogía |
| :--- | :--- | :--- |
| `pwd` | ¿Dónde estoy? | GPS / Mapa "Usted está aquí" |
| `ls` | ¿Qué hay aquí? | Encender la luz |
| `ls -a` | Mostrar ocultos | Gafas de Rayos X |
| `ls -lah`| Mostrar todo detallado | Informe forense |
| `cd carpeta` | Entrar en carpeta | Abrir una puerta |
| `cd ..` | Subir un nivel | Salir de la habitación |
| `cd` | Ir a casa | Botón "Home" |
| `cd -` | Ir al anterior | Botón "Last Channel" |
| **TAB** | Autocompletar | El Autocorrector (pero bueno) |

¡Felicidades! Ya no eres un turista perdido. Eres un explorador con mapa y brújula. La terminal es tuya.