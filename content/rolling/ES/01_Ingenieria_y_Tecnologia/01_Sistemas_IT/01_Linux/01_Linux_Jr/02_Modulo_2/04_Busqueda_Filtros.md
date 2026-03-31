@title: Buscar archivos y filtrar texto: find, grep, locate
@icon: 🕵️‍♂️
@description: Localizar archivos por criterios y extraer líneas por patrón; base para administración y LPIC-1.
@order: 4

# Búsqueda de archivos y filtros de texto: find, grep y locate

Bienvenido al Módulo 2, Lección 4.

Hasta ahora, hemos asumido que sabes dónde están tus archivos. Haces `cd Documentos` porque sabes que están ahí. Pero, ¿qué pasa cuando **no lo sabes**?

Imagina este escenario:
*   Eres el administrador de un servidor.
*   El disco duro se ha llenado misteriosamente al 100%.
*   No sabes qué archivo está ocupando el espacio.
*   No sabes dónde está.
*   Y para colmo, tu jefe te pide que encuentres "ese archivo de configuración que contenía la palabra 'password' que creamos hace 3 años".

Si intentas buscar esto abriendo carpetas una por una (`cd`, `ls`, `cd`, `ls`...), tardarás una vida entera. Un sistema Linux moderno tiene cientos de miles de archivos.

Necesitas herramientas de **búsqueda forense**.
En esta lección masiva, vamos a convertirte en un sabueso digital. Aprenderás a localizar archivos por su nombre, su tamaño, su fecha de creación o incluso por el texto que contienen en su interior.

Esta guía está dividida en tres partes fundamentales:
1.  **Búsqueda Rápida:** `locate` y Comodines.
2.  **Búsqueda Profunda:** El todopoderoso `find`.
3.  **Búsqueda de Contenido:** El legendario `grep`.

Prepárate. Vamos a excavar.

@section: Parte 1: Los Comodines (Globbing)

Antes de usar comandos de búsqueda, debes entender cómo la Shell (Bash) te ayuda a referirte a grupos de archivos. Esto se llama "Globbing".
Esto no es un comando; es una característica de la propia terminal que funciona con *cualquier* comando (`ls`, `rm`, `cp`).

### El Asterisco (`*`)
El asterisco es el comodín definitivo. Significa: **"Cualquier cosa, de cualquier longitud"**.

*   `*.jpg`: Todo lo que acabe en .jpg.
*   `foto*`: Todo lo que empiece por foto.
*   `*vacaciones*`: Todo lo que tenga la palabra "vacaciones" en medio, al principio o al final.
*   `*`: Todo. Absolutamente todo.

**Ejemplo Práctico:**
```bash
# Listar todos los archivos PNG
$ ls *.png

# Borrar todos los archivos que empiezan por 'borrador'
$ rm borrador*
```

### La Interrogación (`?`)
Es más preciso que el asterisco. Significa: **"Cualquier carácter, pero SOLO UNO"**.

Imagina que tienes: `foto1.jpg`, `foto2.jpg`, `foto10.jpg`.
*   `ls foto*.jpg`: Muestra los tres (porque `*` acepta cualquier longitud).
*   `ls foto?.jpg`: Muestra solo `foto1.jpg` y `foto2.jpg`. NO muestra `foto10.jpg` porque "10" son dos caracteres, y `?` solo vale por uno.

### Los Corchetes (`[]`)
Esto es para francotiradores. Significa: **"Uno de los caracteres que hay dentro"**.

*   `ls foto[123].jpg`: Busca `foto1.jpg`, `foto2.jpg` o `foto3.jpg`. No busca `foto4.jpg`.
*   **Rangos:** Puedes usar guiones.
    *   `[0-9]`: Cualquier número del 0 al 9.
    *   `[a-z]`: Cualquier letra minúscula.
    *   `[A-Z]`: Cualquier letra mayúscula.

**Ejemplo de precisión:**
Quiero listar archivos que sean `data_a.txt` o `data_b.txt`, pero no `data_c.txt`.
```bash
$ ls data_[ab].txt
```

### Las Llaves (`{}`) - Expansión
Aunque técnicamente no es "globbing" sino "expansión", se usa igual. Sirve para generar secuencias.
```bash
# Crear carpetas Año_2023, Año_2024, Año_2025
$ mkdir Año_{2023..2025}

# Listar archivos con dos extensiones diferentes a la vez
$ ls *.{jpg,png}
```

@quiz: Tienes los archivos `img1.png`, `img2.png`, `img10.png` y `imgA.png`. Si ejecutas `ls img[0-9].png`, ¿cuáles se mostrarán?
@option: Todos.
@option: img1.png, img2.png y img10.png.
@correct: Solo img1.png y img2.png.
@option: Solo img10.png.

@section: Parte 2: `locate` (El Índice Rápido)

A veces sabes el nombre del archivo y lo quieres YA. No quieres esperar a que el ordenador escanee el disco duro.
Para eso existe `locate`.

### ¿Cómo funciona?
`locate` no busca en tu disco duro en tiempo real. Eso sería lento.
`locate` busca en una **Base de Datos** (un índice gigante) que el sistema mantiene con la lista de todos los archivos.
Es como buscar en el índice de un libro en lugar de leer todas las páginas.

**Ventaja:** Es instantáneo. Tarda milisegundos en encontrar entre millones de archivos.
**Desventaja:** El índice puede estar desactualizado. Si creaste un archivo hace 1 minuto, `locate` no lo encontrará porque aún no está en el índice.

### Uso Básico
```bash
$ locate pasaportes
/home/usuario/Documentos/pasaportes_scan.pdf
/home/usuario/viajes/pasaportes.txt
```

### Actualizando el Índice (`updatedb`)
El índice se actualiza automáticamente una vez al día (normalmente de madrugada).
Si acabas de crear un archivo y `locate` no lo encuentra, puedes forzar la actualización manual del índice.
(Requiere permisos de administrador).

```bash
$ sudo updatedb
```
Espera unos segundos y prueba `locate` de nuevo.

@section: Parte 3: `find` (El Buscador Absoluto)

Si `locate` es rápido pero tonto, `find` es lento pero **omnipotente**.
`find` recorre el disco duro real, archivo por archivo, en tiempo real. No usa índices. Es la herramienta más precisa y potente del arsenal de Linux.

La sintaxis de `find` es diferente a la de otros comandos y suele asustar a los novatos. Vamos a desmitificarla.

### Anatomía de `find`
```bash
find [DÓNDE] [CRITERIOS] [ACCIÓN]
```
1.  **DÓNDE:** ¿En qué carpeta empiezo a buscar? (Si no pones nada, busca en la actual `.`).
2.  **CRITERIOS:** ¿Qué estás buscando? (Nombre, tamaño, fecha...).
3.  **ACCIÓN:** (Opcional) ¿Qué hago cuando lo encuentre? (Por defecto: imprimirlo en pantalla).


### 3.1 Búsqueda por Nombre

El criterio más común. Usamos `-name`.

```bash
# Buscar 'informe.txt' en la carpeta actual y subcarpetas
$ find . -name "informe.txt"

# Buscar en TODO el sistema (necesita sudo si buscas en carpetas protegidas)
$ sudo find / -name "hosts"
```

**Sensibilidad a Mayúsculas:**
`-name` distingue mayúsculas. "Foto.jpg" no es "foto.jpg".
Si quieres buscar ignorando mayúsculas, usa `-iname` (Insensitive Name).
```bash
$ find . -iname "FOTO.JPG"
# Encontrará: Foto.jpg, foto.jpg, FOTO.JPG...
```

**Usando Comodines:**
Siempre que uses comodines con `find`, **PON COMILLAS**. Si no, la shell intentará expandirlos antes de que `find` los vea y dará error.
```bash
# Incorrecto (a menudo falla)
$ find . -name *.jpg

# CORRECTO
$ find . -name "*.jpg"
```


### 3.2 Búsqueda por Tipo

A veces un directorio se llama igual que un archivo. Puedes filtrar qué tipo de objeto buscas con `-type`.
*   `f`: File (Archivo normal).
*   `d`: Directory (Directorio/Carpeta).
*   `l`: Link (Enlace simbólico).

```bash
# Buscar carpetas que se llamen 'config'
$ find /etc -type d -name "config"

# Buscar archivos que se llamen 'python' (para no encontrar la carpeta python)
$ find /usr -type f -name "python"
```


### 3.3 Búsqueda por Tamaño (Vital para limpiar disco)

Este es el superpoder de `find`. Puedes buscar archivos basándote en lo que ocupan.
Usamos `-size`. Los modificadores son:
*   `k`: Kilobytes.
*   `M`: Megabytes.
*   `G`: Gigabytes.
*   `+`: Mayor que.
*   `-`: Menor que.

**Ejemplos Reales:**
```bash
# Encontrar archivos gigantes (más de 1 Gigabyte) en mi home
# ¡Útil para liberar espacio!
$ find ~ -size +1G

# Encontrar archivos vacíos (0 bytes)
$ find . -size 0

# Encontrar archivos que pesan EXACTAMENTE 10 Megas
$ find . -size 10M

# Encontrar archivos de entre 100MB y 1GB (Combinando criterios)
$ find . -size +100M -size -1G
```


### 3.4 Búsqueda por Tiempo (El Forense)

¿Recuerdas que editaste un archivo ayer pero no sabes cuál? `find` lo sabe.
Linux guarda tres fechas para cada archivo:
1.  **mtime (Modification Time):** Cuándo se cambió el contenido.
2.  **atime (Access Time):** Cuándo se leyó/abrió por última vez.
3.  **ctime (Change Time):** Cuándo cambiaron sus metadatos (permisos, nombre).

La unidad son **días** (para horas se usa `-mmin`).
*   `-7`: Hace menos de 7 días.
*   `+7`: Hace más de 7 días.
*   `7`: Hace exactamente 7 días.

**Ejemplos:**
```bash
# Archivos modificados en las últimas 24 horas (el día 0)
$ find . -mtime -1

# Archivos modificados hace más de 30 días (viejos)
$ find /var/log -mtime +30

# Archivos a los que se accedió hace menos de 10 minutos (usando minutos)
$ find . -amin -10
```


### 3.5 Búsqueda por Usuario o Permisos

Útil para auditores de seguridad.

```bash
# Encontrar archivos que pertenecen al usuario 'juan'
$ find /home -user juan

# Encontrar archivos con permisos 777 (peligrosos, todo el mundo puede escribir)
$ find . -perm 777
```


### 3.6 Operadores Lógicos (AND, OR, NOT)

Puedes combinar todo lo anterior.
*   Por defecto, si pones varios criterios, es un **AND** (se deben cumplir todos).
*   `-o`: **OR** (se cumple uno u otro).
*   `-not` o `!`: **NOT** (invierte la condición).

```bash
# Buscar archivos .jpg O .png
$ find . -name "*.jpg" -o -name "*.png"

# Buscar archivos que NO sean del usuario root
$ find . -not -user root
```


### 3.7 La Acción `-exec` (Peligro y Poder)

Hasta ahora, `find` solo nos lista los archivos. Pero, ¿y si queremos *hacer* algo con ellos?
Por ejemplo: "Busca todos los .jpg y muévelos a la carpeta Fotos". O "Busca los archivos temporales y bórralos".

Podríamos hacerlo a mano uno por uno, pero `find` tiene la opción `-exec`.
Esta opción ejecuta un comando sobre cada archivo que encuentra.

**Sintaxis Extraña:**
`... -exec COMANDO {} \;`
*   `{}`: Es un marcador de posición. `find` sustituye esto por el nombre del archivo encontrado.
*   `\;`: Indica que el comando ha terminado. Es obligatorio escapar el punto y coma.

**Ejemplo: Borrado Masivo (¡CUIDADO!)**
```bash
# Buscar archivos .tmp y borrarlos
$ find . -name "*.tmp" -exec rm {} \;
```
*Traducción:* "Por cada archivo .tmp que encuentres, ejecuta `rm [archivo]`".

**Ejemplo: Cambiar Permisos**
```bash
# Buscar todos los scripts .sh y hacerlos ejecutables
$ find . -name "*.sh" -exec chmod +x {} \;
```

**Consejo de Seguridad:**
Antes de ejecutar un comando destructivo con `-exec`, ejecuta primero el `find` sin el `-exec` para ver qué archivos va a encontrar. Asegúrate de que la lista es correcta.

@quiz: Quieres encontrar todos los archivos en tu carpeta actual que ocupen más de 500 Megabytes. ¿Qué comando usas?
@option: find . -size 500
@option: locate +500M
@correct: find . -size +500M
@option: grep -size 500M

@section: Parte 4: `grep` (Buscando Dentro de Archivos)

`find` busca archivos por sus atributos (nombre, tamaño).
`grep` busca **dentro** del contenido de los archivos. Es el buscador de texto definitivo.

Su nombre viene de **G**lobal **R**egular **E**xpression **P**rint.

### Uso Básico
```bash
grep "texto_a_buscar" archivo
```

Ejemplo: Buscar si tengo un usuario llamado "pepe" en el sistema.
```bash
$ grep "pepe" /etc/passwd
```
Si no sale nada, pepe no existe. Si sale una línea, pepe está ahí.

### Búsqueda Recursiva (`-r`)
Esto es lo que usarás el 99% de las veces. Quieres buscar una palabra en **todos** los archivos de una carpeta y sus subcarpetas.

Imagina que eres programador y quieres saber en qué archivo de tu proyecto usaste la función `calcular_iva`.
```bash
$ grep -r "calcular_iva" .
./src/main.py: def calcular_iva(precio):
./src/utils.py: from main import calcular_iva
```
`grep` te dice el archivo y te muestra la línea.

### Opciones Vitales de `grep`

1.  **Ignorar Mayúsculas (`-i`):**
    Busca "error", "ERROR", "Error"...
    ```bash
    $ grep -i "error" log.txt
    ```

2.  **Número de Línea (`-n`):**
    Te dice en qué línea está el texto. Imprescindible para editar código.
    ```bash
    $ grep -n "TODO" main.c
    45: // TODO: Arreglar este bug
    ```

3.  **Invertir Búsqueda (`-v`):**
    Muestra todas las líneas que **NO** contienen el texto.
    *Caso de uso:* Ver un archivo de configuración sin los comentarios. (Los comentarios en Linux suelen empezar por `#`).
    ```bash
    $ grep -v "^#" /etc/ssh/sshd_config
    ```

4.  **Solo el nombre del archivo (`-l`):**
    Si hay muchas coincidencias, a veces no quieres ver el texto, solo saber qué archivos lo contienen.
    ```bash
    $ grep -rl "virus" /home
    ```

5.  **Contexto (`-C`):**
    A veces encontrar la línea no basta. Quieres ver qué pasó antes y después.
    *   `-C 2`: Muestra 2 líneas de contexto (antes y después).
    *   `-A 2` (After): 2 líneas después.
    *   `-B 2` (Before): 2 líneas antes.
    ```bash
    $ grep -C 3 "CRITICAL ERROR" server.log
    ```

### Introducción a Expresiones Regulares (Regex) con grep
`grep` soporta patrones de búsqueda avanzados llamados Regex. Son un lenguaje en sí mismos, pero aquí tienes los dos más útiles:

*   `^` (Circunflejo): Significa **"Principio de línea"**.
    `grep "^root" /etc/passwd` -> Busca líneas que *empiecen* por root.
*   `$` (Dólar): Significa **"Final de línea"**.
    `grep "bash$" /etc/passwd` -> Busca líneas que *terminen* en bash.

@quiz: Quieres buscar la palabra "configuración" dentro de todos los archivos de tu carpeta actual y subcarpetas, ignorando si está en mayúsculas o minúsculas. ¿Qué comando es el correcto?
@option: find . -name "configuración"
@option: grep -r "configuración" .
@correct: grep -ri "configuración" .
@option: locate configuración

@section: Parte 5: Combinando Poderes (Pipes)

El verdadero poder de Linux surge al conectar estos comandos.

**Ejemplo 1: Contar resultados**
¿Cuántos archivos JPG tengo?
```bash
find . -name "*.jpg" | wc -l
```
(`find` busca, `wc -l` cuenta las líneas del resultado).

**Ejemplo 2: Buscar en los resultados**
Encontrar procesos de java y luego buscar cuáles son del usuario "admin".
```bash
ps aux | grep "java" | grep "admin"
```

**Ejemplo 3: Buscar un comando**
A veces quieres saber dónde está instalado un programa.
*   `which python`: Te dice la ruta del ejecutable principal (`/usr/bin/python`).
*   `whereis python`: Te dice el ejecutable, el código fuente y el manual.

@section: Parte 6: sed y awk (nivel certificación LPIC-1)

`grep` encuentra líneas; **`sed`** edita flujos de texto en pipeline; **`awk`** divide líneas en campos y puede sumar, filtrar y formatear. En exámenes y entrevistas te pedirán **una línea** que haga algo útil.

### sed (stream editor)

*   Sustituir la primera ocurrencia en cada línea:
    ```bash
    sed 's/viejo/nuevo/' archivo.txt
    ```
*   Sustituir **todas** las ocurrencias en la línea:
    ```bash
    sed 's/foo/bar/g' archivo.txt
    ```
*   Borrar líneas que coincidan:
    ```bash
    sed '/^#/d' /etc/nginx/nginx.conf
    ```
*   Imprimir solo un rango de líneas:
    ```bash
    sed -n '10,20p' /var/log/syslog
    ```

**RHEL vs Debian:** `sed` es idéntico; lo que cambia es **qué archivo** editas.

### awk (patrón { acción })

**Modelo mental:** cada línea se parte por espacios (o por `-F:`) en `$1`, `$2`, ...

*   Imprimir la primera columna de `ps`:
    ```bash
    ps aux | awk '{print $1}'
    ```
*   Sumar la quinta columna (tamaño en `ls -l`):
    ```bash
    ls -l | awk '{s+=$5} END {print s}'
    ```
*   Filtrar líneas donde el campo 3 sea mayor que 100:
    ```bash
    awk '$3 > 100' mediciones.txt
    ```

Para profundizar: `man awk`, `info gawk`. En certificación, practica **memorizar** la sintaxis de `sed 's///g'` y `awk '{print $N}'`.

@quiz: ¿Qué comando de `sed` sustituye todas las apariciones de "foo" por "bar" en cada línea?
@option: sed 's/foo/bar/' archivo
@correct: sed 's/foo/bar/g' archivo
@option: awk '/foo/bar/g'

@quiz: En awk, ¿qué representa $1 en una línea por defecto?
@option: El último campo
@correct: El primer campo (según el separador de campos)
@option: El número de línea

@section: Laboratorio Práctico: El Caso del Servidor Lleno

Vamos a simular una situación real de administrador.

1.  **El problema:** Tu carpeta personal parece llena.
2.  **Paso 1:** Usar `find` para localizar los culpables (archivos de más de 50MB).
    ```bash
    $ find ~ -size +50M
    ```
3.  **Paso 2:** Ves que hay muchos archivos `.log` gigantes. Quieres comprobar si son importantes. Buscas la palabra "Error" en ellos.
    ```bash
    $ grep "Error" *.log
    ```
4.  **Paso 3:** Confirmas que son basura. Decides borrarlos.
    ```bash
    # Primero listamos para estar seguros
    $ find ~ -name "*.log" -size +50M
    # Luego ejecutamos el borrado
    $ find ~ -name "*.log" -size +50M -exec rm {} \;
    ```

¡Felicidades! Has limpiado el sistema usando búsqueda avanzada.

@section: Resumen / Cheat Sheet

| Herramienta | Uso Ideal | Comando Ejemplo |
| :--- | :--- | :--- |
| **Comodines** | Seleccionar múltiples archivos en el directorio actual | `ls *.jpg` |
| **locate** | Búsqueda instantánea de archivos por nombre (índice) | `locate archivo` |
| **find** | Búsqueda precisa y en tiempo real (tamaño, fecha, tipo) | `find . -name "*.txt"` |
| **grep** | Buscar texto **DENTRO** de archivos | `grep -r "texto" .` |
| **which** | Encontrar dónde está instalado un programa | `which firefox` |

Ahora tienes visión de rayos X sobre tu sistema. Ya nada puede esconderse de ti.