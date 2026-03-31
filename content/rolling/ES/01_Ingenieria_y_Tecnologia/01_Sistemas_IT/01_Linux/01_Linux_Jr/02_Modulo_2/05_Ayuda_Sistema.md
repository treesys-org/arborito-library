@title: Ayuda integrada: man, info, --help y apropos
@icon: 🆘
@description: Usar la documentación del sistema para resolver dudas sin salir de la terminal.
@order: 5

# Ayuda en Linux: man, info, --help y apropos

Bienvenido a la lección más importante de tu carrera como usuario de Linux.

Sí, sé que he dicho eso en otras lecciones. Pero esta vez es diferente. En las lecciones anteriores te enseñé a *pescar* peces específicos (mover archivos, crear carpetas). En esta lección, te voy a enseñar **cómo fabricar tu propia caña de pescar**.

### El Mito del "Gurú de Linux"
Existe una leyenda urbana de que los expertos en Linux ("Gurús" o "Barbasgrises") son genios con una memoria fotográfica que recuerdan los 25.000 comandos del sistema y sus 100.000 opciones.

**Eso es mentira.**

Nadie, absolutamente nadie, memoriza todo esto. La capacidad de almacenamiento del cerebro humano es limitada y es mejor usarla para recordar el cumpleaños de tu pareja o la trama de tu serie favorita.

La diferencia entre un **Novato** y un **Experto** no es lo que saben de memoria.
La diferencia es que, cuando el Experto no sabe algo (que es el 90% de las veces), **sabe exactamente dónde buscar la respuesta en menos de 5 segundos**, sin salir de la terminal y sin abrir Google.

En Windows, cuando te pierdes, buscas en Google o llamas al soporte técnico.
En Linux, el sistema viene con el manual de instrucciones completo preinstalado. Es como si tu coche viniera con un mecánico en el maletero.

En esta guía masiva, vamos a aprender a hablar con ese mecánico.

@section: 1. La Filosofía RTFM

Antes de teclear, hablemos de cultura. Si entras en un foro de Linux o un chat de IRC y haces una pregunta básica (tipo *"¿Cómo copio un archivo?"*), es probable que alguien te responda con cuatro letras, a veces de forma ruda:

**RTFM**

Significa: **"Read The F***ing Manual"*** (Lee El Jod*** Manual).

Aunque la forma es agresiva, el fondo tiene una verdad profunda: **La autosuficiencia es una virtud.**
Linux respeta a quien intenta ayudarse a sí mismo primero. Antes de preguntar a otro humano (cuyo tiempo es valioso), debes preguntar a la máquina (cuyo tiempo es infinito).

El sistema de ayuda de Linux es jerárquico. Tienes que seguir este orden de escalada:
1.  **Ayuda Rápida (`--help`):** Para refrescar la memoria sobre una opción.
2.  **El Manual (`man`):** Para entender cómo funciona un comando a fondo.
3.  **Búsqueda (`apropos`):** Cuando no sabes qué comando usar.
4.  **Info (`info`):** Para estudiar programas complejos como un libro.
5.  **Documentación Local (`/usr/share/doc`):** Para configuraciones avanzadas.
6.  **Internet:** El último recurso.

Vamos a dominar cada nivel.

@section: Nivel 1: El Salvavidas Rápido (`--help`)

Estás escribiendo un comando. Sabes que `ls` lista archivos. Pero quieres que los ordene por tamaño y no recuerdas si la letra era `-S`, `-s` o `-size`. No quieres leer un libro entero, solo quieres el dato rápido.

Aquí entra la bandera universal: `--help` (o a veces `-h`).

Casi el 99% de los comandos en Linux aceptan esta opción.

```bash
$ ls --help
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
      --author               with -l, print the author of each file
  -b, --escape               print C-style escapes for nongraphic characters
...
```

### Cómo leer la sintaxis de ayuda
La primera línea es crítica. Se llama "Usage" (Uso) y tiene una gramática propia que debes aprender:

`Usage: comando [OPCIONAL] <OBLIGATORIO>`

1.  **Corchetes `[]`**: Significan que lo de dentro es **Opcional**. Puedes usarlo o no.
2.  **Picos `<>` o sin nada**: Significan que es **Obligatorio**.
3.  **Puntos suspensivos `...`**: Significan que puedes poner **varios** (una lista).
4.  **Barra vertical `|`**: Significa **O** (uno u otro, pero no ambos).

**Ejemplo de análisis:**
`Usage: cp [OPTION]... [-T] SOURCE DEST`

*   `cp`: El comando.
*   `[OPTION]...`: Puedes poner opciones (como `-r` o `-v`) o no poner ninguna. Los puntos significan que puedes poner muchas (`-rv`).
*   `SOURCE`: Es el archivo origen. No tiene corchetes, así que **tienes** que ponerlo obligatoriamente.
*   `DEST`: Es el destino. También obligatorio.

**Opciones Cortas vs. Largas:**
Fíjate en esta línea del ejemplo de `ls`:
`-a, --all`

*   **`-a` (Corta):** Usa un solo guion y una sola letra. Es rápido de escribir. Puedes combinarlas (`-lah`).
*   **`--all` (Larga):** Usa dos guiones y una palabra completa. Es más legible para scripts. No se pueden combinar (`--allhuman` no funciona).

**Ejercicio Mental:**
Si ves `Usage: grep [OPTIONS] PATTERN [FILE...]`, ¿qué significa?
*   ¿Puedo usar grep sin opciones? Sí (corchetes).
*   ¿Puedo usar grep sin patrón? No (sin corchetes).
*   ¿Puedo usar grep sin archivo? Sí (corchetes). En ese caso, leerá del teclado.
*   ¿Puedo poner 50 archivos? Sí (puntos suspensivos).

@section: Nivel 2: El Libro Sagrado (`man`)

`--help` está bien para un recordatorio rápido. Pero si quieres **entender** el comando, necesitas el Manual.
Las "Páginas Man" (man pages) son la enciclopedia de Linux.

```bash
$ man ls
```

Esto abrirá un lector (paginador) con la documentación completa.

### Navegación dentro de `man`
Como `man` usa el programa `less` por debajo, los controles son los mismos que aprendiste en la lección de visualización de archivos:

*   **Flechas / Enter:** Bajar línea a línea.
*   **Espacio:** Bajar una página entera.
*   **b:** Subir una página entera (Back).
*   **/**: Buscar texto (ej: escribe `/sort` y pulsa Enter para buscar cómo ordenar).
*   **n:** Ir a la siguiente coincidencia de la búsqueda.
*   **q:** Salir (Quit). ¡Importante!

### Anatomía de una Página Man
Todas las páginas siguen una estructura rígida y estandarizada:

1.  **NAME:** Nombre y una descripción de una línea.
2.  **SYNOPSIS:** La sintaxis técnica (como vimos en `--help`).
3.  **DESCRIPTION:** La explicación larga de qué hace el programa.
4.  **OPTIONS:** La lista detallada de cada bandera (`-a`, `-l`, etc.) y qué hace exactamente.
5.  **EXAMPLES:** (A veces). Ejemplos de uso real. ¡Muy valioso!
6.  **FILES:** Qué archivos de configuración usa este programa.
7.  **SEE ALSO:** Otros comandos relacionados que te podrían interesar.

### Las Secciones del Manual (El Secreto de los Números)
A veces, al hacer `man algo`, verás un número entre paréntesis, como `passwd(1)` o `passwd(5)`.
O verás que hay dos cosas que se llaman igual: el comando `passwd` (para cambiar tu contraseña) y el archivo `/etc/passwd` (que guarda los usuarios).

Si escribes `man passwd`, ¿cuál de los dos te enseña?
Por defecto, el comando. Pero Linux organiza el manual en **Secciones Numeradas** para evitar colisiones.

**Las secciones vitales:**
1.  **Comandos de Usuario:** Programas normales (`ls`, `cp`, `passwd` comando).
2.  **Llamadas al Sistema:** Funciones del Kernel para programadores (avanzado).
3.  **Funciones de Librería:** Para programadores en C (`printf`).
4.  **Dispositivos:** Archivos especiales en `/dev`.
5.  **Formatos de Archivo:** ¡Súper útil! Explica la sintaxis de archivos de configuración (`passwd` archivo, `fstab`).
6.  **Juegos:** Sí, los juegos tienen manual.
7.  **Miscelánea.**
8.  **Comandos de Administración:** Herramientas de root (`fdisk`, `ifconfig`, `useradd`).

**Cómo viajar entre secciones:**
Si quieres ver la ayuda del *archivo* de contraseñas y no del comando, debes especificar la sección 5:

```bash
$ man 5 passwd
```

Si no sabes en qué secciones está algo, usa el flag `-f` (o el comando `whatis`):
```bash
$ whatis passwd
passwd (1)           - change user password
passwd (1ssl)        - compute password hashes
passwd (5)           - the password file
```
Ahí ves que existe en la 1 y en la 5.

@quiz: Estás editando el archivo `/etc/fstab` y no recuerdas el formato de las columnas. ¿Qué comando te dará la documentación específica sobre el formato de ese archivo?
@option: man fstab
@option: man 1 fstab
@correct: man 5 fstab
@option: help fstab

@section: Nivel 3: El Buscador (`apropos`)

El problema de `man` es que necesitas saber el nombre del comando.
Pero, ¿qué pasa si quieres "crear una partición" pero no sabes que el comando se llama `fdisk`?

Aquí es donde entra `apropos`.
`apropos` busca en las descripciones de todos los manuales palabras clave.

```bash
# ¿Cómo se llama eso para particionar?
$ apropos partition
```

El sistema te devolverá una lista:
```
addpart (8)          - tell the kernel about the existence of a partition
fdisk (8)            - manipulate disk partition table
partx (8)            - tell the kernel about the presence and numbering of on-disk partitions
...
```
¡Ajá! `fdisk` parece lo que busco ("manipulate disk partition table"). Ahora puedo hacer `man fdisk`.

**Consejo:** Si `apropos` no te devuelve nada o se queja, es posible que la base de datos de manuales no esté indexada. Puedes actualizarla (como root) con el comando `mandb` o `updatedb`.

@section: Nivel 4: La Trampa de los "Built-ins"

Aquí hay una trampa en la que caen todos los novatos.
Intentas buscar ayuda sobre el comando `cd` (Change Directory).

Escribes:
```bash
$ man cd
No manual entry for cd
```
*(Nota: En algunas distros sí hay entrada, pero en muchas no).*

**¿Cómo es posible?** `cd` es el comando más básico. ¿No tiene manual?

La razón es técnica pero fascinante.
Hay dos tipos de comandos:
1.  **Ejecutables (Binarios):** Son archivos reales en tu disco duro (ej: `/usr/bin/ls`). `man` documenta estos archivos.
2.  **Integrados (Built-ins):** Son funciones que viven *dentro* de la propia Shell (Bash). No son un archivo aparte. Como `cd` tiene que cambiar el estado de la propia terminal, tiene que ser parte de ella.

Para saber si un comando es un archivo o un built-in, usa el comando `type`:
```bash
$ type ls
ls is aliased to `ls --color=auto'

$ type /bin/ls
/bin/ls is /bin/ls

$ type cd
cd is a shell builtin
```

**Cómo pedir ayuda para Built-ins:**
Si `man` no funciona, usa el comando `help` (que es otro built-in de Bash).

```bash
$ help cd
cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.
    ...
```

**Resumen de la trampa:**
*   Si es un programa (`ls`, `grep`): Usa `man`.
*   Si es parte de la shell (`cd`, `alias`, `history`, `if`): Usa `help`.

@section: Nivel 5: La Biblioteca de Alejandría (`info`)

Si `man` se queda corto, existe un nivel superior.
El proyecto GNU (los creadores de muchas herramientas de Linux) decidieron que las páginas `man` eran demasiado limitadas para programas complejos. Inventaron el sistema **Info**.

Los documentos `info` no son páginas planas. Son **Libros con Hipervínculos**. Tienen capítulos, subcapítulos e índices.

```bash
$ info coreutils
```

**Navegación en Info (Es un poco extraña):**
La navegación en `info` es anterior a la web, así que no usa el ratón.
*   **Tab:** Moverse al siguiente hipervínculo (marcado con `*`).
*   **Enter:** Entrar en el enlace seleccionado.
*   **n:** Ir al siguiente nodo (página/capítulo) al mismo nivel.
*   **p:** Ir al nodo previo.
*   **u:** Subir un nivel (Up) hacia el índice principal.
*   **q:** Salir.

Hoy en día, `info` se usa menos porque la gente prefiere buscar la documentación HTML en internet, pero si estás en un servidor sin internet, `info` es la documentación más completa y profunda que existe en tu disco duro.

@section: Nivel 6: La Documentación Oculta (`/usr/share/doc`)

Hay un lugar en tu disco duro que es como el ático polvoriento donde los autores de los programas dejan notas.
Es el directorio `/usr/share/doc`.

Por cada programa que instalas, se crea una carpeta aquí.
```bash
$ cd /usr/share/doc
$ ls
```
Entra en la carpeta de algún programa complejo, por ejemplo `apt` o `cron`.
Dentro encontrarás tesoros que no están en el manual:
1.  **README:** Léeme primero. Información general.
2.  **CHANGELOG:** Historial de cambios. ¿Qué hay nuevo en esta versión?
3.  **examples:** (A veces). Una carpeta llena de archivos de configuración de ejemplo.
    *   *Caso de uso:* Estás configurando un servidor DHCP y no sabes la sintaxis. Copias el archivo de `/usr/share/doc/isc-dhcp-server/examples/dhcpd.conf.example` a `/etc/dhcp/dhcpd.conf` y lo editas. ¡Te ahorra horas de escribir!

@section: Nivel 7: Herramientas de la Comunidad (tldr, cheat)

La comunidad de Linux sabe que las páginas `man` son áridas y difíciles de leer.
Por eso han nacido proyectos modernos para darte "Chuletas" (Cheat Sheets) instantáneas.

Estas herramientas no suelen venir preinstaladas, pero deberías instalarlas.

### `tldr` (Too Long; Didn't Read)
Es un cliente que te muestra **solo ejemplos prácticos**.
Mientras `man tar` te explica la historia de las cintas magnéticas, `tldr tar` te dice:
*   "Para comprimir: `tar -czvf archivo.tar.gz carpeta`"
*   "Para descomprimir: `tar -xzvf archivo.tar.gz`"

### `cheat`
Similar a tldr, pero permite crear tus propias chuletas locales.

@section: Nivel 8: El Arte de Buscar en Google

A veces, el manual no tiene la respuesta porque tu problema es un error específico ("Error 500 al iniciar Apache").
Aquí es donde vas a Internet. Pero hay que saber buscar.

**Jerarquía de Fuentes Fiables:**

1.  **Arch Wiki:** La Biblia de Internet. Aunque no uses Arch Linux, su documentación es la mejor del mundo. Si quieres saber cómo funciona el WiFi en Linux, busca "Arch Wiki NetworkManager". Es técnico, preciso y actualizado.
2.  **Gentoo Wiki:** Similar a Arch, muy técnico y detallado.
3.  **StackOverflow / Unix & Linux StackExchange:** Para preguntas específicas de programación o errores. Mira las respuestas con el "tick" verde, pero lee los comentarios.
4.  **DigitalOcean Tutorials:** Sorprendentemente, esta empresa de hosting tiene los mejores tutoriales paso a paso para configurar servidores (Nginx, Docker, etc.) para principiantes.
5.  **Foros de tu Distro (Ubuntu Forums, etc.):** Útil, pero a veces la información es antigua. Fíjate siempre en la **fecha** del post. Una solución de 2012 para el Wifi seguramente romperá tu sistema de 2025.

**Cómo buscar errores:**
*   Copia el mensaje de error EXACTO. Ponlo entre comillas en Google.
    `"Kernel panic - not syncing: VFS: Unable to mount root fs"`
*   Añade tu distribución si es relevante.
    `site:askubuntu.com "wifi not working"`

@section: Taller Práctico: Resuelve el Misterio

Vamos a jugar a un juego. Tienes que usar las herramientas que has aprendido. No vale mirar la solución abajo.

**Misión 1:** No recuerdo cómo se llama el comando para cambiar la contraseña de un usuario.
*   *Herramienta:* `apropos`
*   *Acción:* `apropos password`
*   *Resultado:* Buscarás en la lista y encontrarás `passwd`.

**Misión 2:** Quiero usar `ls` pero quiero que los directorios aparezcan primero, antes que los archivos. No sé la opción.
*   *Herramienta:* `man` o `--help`
*   *Acción:* `man ls` y buscar (`/`) la palabra "directory". O "group".
*   *Resultado:* Encontrarás `--group-directories-first`.

**Misión 3:** ¿Cuál es el archivo de configuración del sistema para los servidores DNS? Sé que tiene que ver con "resolver".
*   *Herramienta:* `apropos` y luego `man` sección 5.
*   *Acción:* `apropos resolver`. Verás `resolv.conf (5)`.
*   *Acción:* `man 5 resolv.conf`.
*   *Resultado:* Te dirá que el archivo es `/etc/resolv.conf` y te explicará su formato.

@section: Resumen / Cheat Sheet

| Herramienta | Cuándo usarla | Comando Ejemplo |
| :--- | :--- | :--- |
| **--help** | Recordatorio rápido de opciones (flags) | `ls --help` |
| **man** | Entender un comando a fondo | `man grep` |
| **man [N]** | Ver formatos de archivo (configuración) | `man 5 passwd` |
| **apropos** | No sé el nombre del comando | `apropos "partition"` |
| **type** | Saber si es programa o built-in | `type cd` |
| **help** | Ayuda para built-ins de la shell | `help cd` |
| **info** | Documentación compleja y navegable | `info coreutils` |
| **tldr** | Solo dame ejemplos, por favor | `tldr tar` |

Felicidades. Ahora tienes el superpoder más importante de todos: **La capacidad de aprender cualquier cosa sin ayuda de nadie.**
Ya no eres un usuario perdido. Eres un investigador.