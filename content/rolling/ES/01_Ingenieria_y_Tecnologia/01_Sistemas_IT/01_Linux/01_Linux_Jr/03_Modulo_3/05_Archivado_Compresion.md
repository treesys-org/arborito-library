@title: Empaquetar y comprimir: tar, gzip, xz, zip
@icon: 📦
@description: Diferencia entre archivo (.tar) y compresión; uso habitual de tar con gzip/xz y formatos zip en Linux.
@order: 5

# Archivado y compresión: tar, gzip, xz y zip

Bienvenido a la lección de logística digital.

Imagina que te mudas de casa. Tienes 500 libros, 2000 fotos en papel y 50 pares de calcetines.
¿Cómo los llevas al camión?
¿Coges los calcetines uno a uno y los tiras dentro del camión? No. Sería un caos. Perderías la mitad y tardarías horas.

Lo que haces es:
1.  **Agrupar:** Metes las cosas en cajas de cartón. Ahora, 50 calcetines son "1 caja".
2.  **Comprimir:** Si es ropa de invierno, usas bolsas de vacío para quitarle el aire y que ocupe menos espacio.

En informática es exactamente igual.
Un servidor web tiene miles de pequeños archivos de configuración, imágenes y scripts. Si quieres enviar todo eso a otro servidor o hacer una copia de seguridad, no puedes enviar 50.000 archivos sueltos. La red se saturará gestionando las cabeceras de cada archivo.

Necesitas **Archivar** (meter en una caja) y **Comprimir** (quitar el aire).

En Windows, estamos malacostumbrados. Hacemos clic derecho, "Enviar a carpeta comprimida (zip)", y el sistema hace las dos cosas a la vez sin decirnos nada.
En Linux (y Unix), estas dos tareas son **operaciones separadas** realizadas por **herramientas separadas**.

En esta guía masiva, vamos a diseccionar la herramienta más antigua y venerada de Unix: **`tar`**. Aprenderemos por qué un `.tar` no comprime nada, por qué existen tantas extensiones raras (`.tar.gz`, `.tar.bz2`, `.tar.xz`) y cómo manejar archivos `.zip` para no enfadar a tus compañeros que usan Windows.

Prepárate. Vamos a empaquetar el mundo.

@section: 1. Conceptos Fundamentales: La Maleta y la Bolsa de Vacío

Antes de escribir comandos, es vital que entiendas la diferencia técnica entre las dos acciones.

### 1.1 Archivado (Archiving)
**Objetivo:** Juntar muchos archivos y carpetas en un solo archivo contenedor.
**Resultado:** Un solo archivo grande.
**Tamaño:** La suma exacta del tamaño de los archivos originales (+ un poquito de metadatos). **No ahorra espacio.**
**Herramienta Reina:** `tar`.

Si tienes 10 fotos de 1MB:
*   Archivas -> Obtienes 1 archivo de 10MB.

### 1.2 Compresión (Compression)
**Objetivo:** Reducir el tamaño de un archivo usando matemáticas (algoritmos) para eliminar redundancia.
**Resultado:** Un archivo ilegible pero más pequeño.
**Herramienta Reina:** `gzip`, `bzip2`, `xz`.

Si tienes 1 archivo de texto de 10MB con muchas palabras repetidas:
*   Comprimes -> Obtienes 1 archivo de 2MB.

### La Filosofía Unix (Haz una cosa bien)
En la filosofía Unix, la herramienta de compresión (`gzip`) solo sabe hacer una cosa: comprimir **UN** archivo. `gzip` no sabe de carpetas. Si intentas comprimir una carpeta con `gzip`, te dará error.

Por eso, el flujo de trabajo en Linux siempre es de dos pasos (aunque a veces se hacen juntos):
1.  Usa `tar` para pegar todos tus archivos en un solo rollo continuo (el archivo `.tar`).
2.  Usa `gzip` para comprimir ese rollo resultante (creando un `.tar.gz`).

@section: 2. `tar`: El Archivador de Cintas

`tar` significa **T**ape **AR**chive (Archivo de Cinta).
Nació en los años 70, cuando las copias de seguridad se hacían en enormes cintas magnéticas de bobina abierta. `tar` se diseñó para leer archivos del disco y escribirlos secuencialmente en la cinta.
Hoy no usamos cintas, pero usamos `tar` exactamente igual: escribimos los archivos secuencialmente en un "archivo contenedor".

### La Sintaxis Sagrada
`tar` es famoso por tener muchas opciones (flags). No necesitas memorizarlas todas, pero sí necesitas memorizar **"Las Tres Funciones"** y **"Los Tres Modificadores"**.

**Las 3 Funciones (Qué vas a hacer):**
Solo puedes elegir una de estas a la vez.
1.  `-c`: **C**reate (Crear un archivo nuevo).
2.  `-x`: e**X**tract (Extraer/Desempaquetar un archivo existente).
3.  `-t`: lis**T** (Listar el contenido sin extraer, como ver el índice).

**Los 2 Modificadores Obligatorios (Casi siempre):**
1.  `-f`: **F**ile (Archivo). Indica que vamos a trabajar con un archivo, no con una cinta física. **IMPORTANTE:** La `f` siempre debe ir al final de las opciones, justo antes del nombre del archivo.
2.  `-v`: **V**erbose (Verboso/Hablador). Le dice a `tar` que nos muestre en pantalla los nombres de los archivos que está procesando. Sin esto, `tar` trabaja en silencio absoluto (filosofía Unix: "si no hay errores, no digas nada").

@section: 3. Creando Archivos (.tar)

Vamos a crear nuestro primer archivo.
Imagina que tienes una carpeta llamada `proyecto` llena de código.

**Comando:**
```bash
$ tar -cvf proyecto.tar proyecto/
```
*   `-c`: Crear.
*   `-v`: Mostrar progreso.
*   `-f`: El nombre del archivo resultante viene a continuación.

**Salida:**
```text
proyecto/
proyecto/main.c
proyecto/header.h
proyecto/logo.png
...
```
Si ahora haces un `ls -lh`, verás un archivo `proyecto.tar`.
Si miras su tamaño, será casi idéntico a la suma de los archivos originales. No hemos comprimido, solo empaquetado.

**¡ADVERTENCIA DE ORDEN!**
Es `tar -cvf archivo_destino origen`.
Si lo haces al revés (`tar -cvf origen archivo_destino`), **destruirás tus datos originales** sobreescribiéndolos con un archivo tar vacío. `tar` es una herramienta antigua y no pregunta "¿Estás seguro?". Ten cuidado.

@section: 4. Comprimiendo sobre la marcha

Ahora que tenemos la caja (`.tar`), queremos quitarle el aire.
Podríamos hacerlo en dos pasos:
1.  `tar -cvf proyecto.tar proyecto/`
2.  `gzip proyecto.tar` (Esto crea `proyecto.tar.gz` y borra el `.tar`).

Pero `tar` es moderno y nos permite usar "plugins" de compresión directamente añadiendo una letra más a los flags.

### 4.1 El Estándar: GZIP (`-z`)
*   **Extensión:** `.tar.gz` o `.tgz`.
*   **Velocidad:** Muy rápida.
*   **Compresión:** Decente.
*   **Uso:** El 95% de las veces usarás esto. Es el estándar de facto en Internet.

**Crear:**
```bash
$ tar -czvf proyecto.tar.gz proyecto/
```
*(Fíjate en la `z` extra).*

### 4.2 El Potente: XZ (`-J`)
*   **Extensión:** `.tar.xz`.
*   **Velocidad:** Muy lenta comprimiendo, rápida descomprimiendo.
*   **Compresión:** Extrema. Reduce el tamaño mucho más que gzip.
*   **Uso:** Para distribuir software (el Kernel de Linux se distribuye así) o para backups que vas a guardar mucho tiempo y donde el espacio es crítico.

**Crear:**
```bash
$ tar -cJvf proyecto.tar.xz proyecto/
```
*(Fíjate en la `J` mayúscula).*

### 4.3 El Viejo: BZIP2 (`-j`)
*   **Extensión:** `.tar.bz2`.
*   **Uso:** Fue popular en los 2000. Comprime más que gzip pero menos que xz. Hoy en día está perdiendo popularidad frente a xz, pero lo verás en sistemas antiguos.

**Crear:**
```bash
$ tar -cjvf proyecto.tar.bz2 proyecto/
```
*(Fíjate en la `j` minúscula).*

@quiz: Tienes una carpeta de logs de 10GB que necesitas guardar para auditoría legal durante 5 años. Quieres que ocupe lo mínimo posible y no te importa que tarde mucho en comprimirse. ¿Qué algoritmo eliges?
@option: gzip (-z)
@option: bzip2 (-j)
@correct: xz (-J)
@option: zip

@section: 5. Extrayendo Archivos

Te has bajado un programa de internet y es un archivo `programa.tar.gz`. ¿Cómo lo abres?

Usamos la función **`-x`** (eXtract).

### Descompresión Inteligente
En versiones modernas de `tar` (GNU tar, el que viene en Linux), **no necesitas decirle qué compresión usa**. `tar` es listo. Detecta si es gzip, bzip2 o xz automáticamente.

Por lo tanto, el comando universal para descomprimir cualquier cosa que empiece por tar es:

```bash
$ tar -xvf archivo_cualquiera.tar.gz
$ tar -xvf archivo_cualquiera.tar.bz2
$ tar -xvf archivo_cualquiera.tar.xz
```
*   `-x`: Extraer.
*   `-v`: Verboso (ver qué sale).
*   `-f`: Archivo.

### El Peligro de la "Bomba Tar" (Tarbomb)
Algunas personas malvadas (o descuidadas) crean archivos tar sin una carpeta contenedora.
Imagina que te bajas `fotos.tar.gz`.
Esperas que al descomprimirlo cree una carpeta `fotos/` y meta todo dentro.
Pero lo ejecutas y... ¡ZAS!
Tu carpeta actual (quizás tu Escritorio o tu Home) se llena de 10.000 imágenes sueltas `img001.jpg`, `img002.jpg`... mezclándose con tus archivos. Limpiar esto es una pesadilla.

**Cómo prevenirlo:**
Antes de extraer, **MIRA** lo que hay dentro.

@section: 6. Mirando sin Tocar (`-t`)

La función **`-t`** (lisT) te permite ver el índice del archivo sin extraer nada. Es como leer la etiqueta de ingredientes.

```bash
$ tar -tvf archivo_sospechoso.tar.gz
```

Salida segura (Bien):
```text
drwxr-xr-x user/user 0 2023-01-01 carpeta_contenedora/
-rw-r--r-- user/user 10 2023-01-01 carpeta_contenedora/foto1.jpg
...
```
Vemos que todo está dentro de `carpeta_contenedora/`. Es seguro extraer.

Salida Tarbomb (Mal):
```text
-rw-r--r-- user/user 10 2023-01-01 foto1.jpg
-rw-r--r-- user/user 10 2023-01-01 foto2.jpg
...
```
Vemos que los archivos están sueltos en la raíz. **¡Peligro!**

### Cómo desactivar una Tarbomb
Si ves que el archivo es una bomba, crea una carpeta de contención y extrae allí.

```bash
$ mkdir zona_segura
$ tar -xvf bomba.tar.gz -C zona_segura/
```
La opción **`-C`** (Change directory) le dice a `tar`: "Cámbiate a esta carpeta antes de empezar a extraer". Es tu escudo protector.

@quiz: Te descargas un archivo `desconocido.tar.gz`. ¿Cuál es el primer comando que deberías ejecutar por seguridad?
@option: tar -xvf desconocido.tar.gz
@correct: tar -tvf desconocido.tar.gz
@option: tar -czvf desconocido.tar.gz
@option: rm desconocido.tar.gz

@section: 7. El Mundo ZIP: Conviviendo con Windows

A veces tienes que enviar archivos a alguien que usa Windows. Si le mandas un `.tar.gz`, te llamará preguntando qué es eso y por qué no puede abrirlo. Windows abre `.zip` de forma nativa.

Linux tiene herramientas para esto: `zip` y `unzip`. (A veces hay que instalarlas: `sudo apt install zip unzip`).

### Comprimiendo en ZIP
Sintaxis: `zip [opciones] archivo_destino.zip archivos_origen`

**Importante:** `zip` no es recursivo por defecto. Si intentas zipear una carpeta sin opciones, solo zipeará la carpeta vacía, no lo de dentro.
Siempre usa **`-r`** (Recursivo).

```bash
# Correcto
$ zip -r trabajo.zip carpeta_trabajo/
```

### Descomprimiendo ZIP
Sintaxis: `unzip archivo.zip`

```bash
$ unzip trabajo.zip
```

**Listar contenido sin extraer:**
```bash
$ unzip -l trabajo.zip
```

**¿Por qué no usamos siempre ZIP en Linux?**
ZIP es un formato antiguo y tiene una desventaja grave en sistemas Unix: **No siempre conserva los permisos de archivo (como los dueños o el bit de ejecutable)** de forma fiable entre sistemas distintos.
`tar` fue diseñado específicamente para preservar todos los metadatos, permisos, dueños y fechas de los archivos Linux. Por eso, para copias de seguridad del sistema o transferencia de software Linux-a-Linux, siempre usamos `tar`. Para transferir fotos a tu tía, usa `zip`.

@section: 8. Casos de Uso Avanzados

### 8.1 Excluyendo Archivos
Imagina que quieres hacer un backup de tu proyecto web, pero no quieres incluir la carpeta `node_modules` (que pesa 200MB y es basura generada) ni los archivos `.git`.

Usa `--exclude`.

```bash
$ tar -czvf backup_web.tar.gz --exclude='node_modules' --exclude='.git' mi_proyecto/
```
*Nota: El patrón de exclusión no debe llevar la ruta completa, solo el nombre.*

### 8.2 Extrayendo un solo archivo
Tienes un backup de 50GB (`backup.tar.gz`) y solo quieres recuperar un archivo que borraste por error (`fotos/perro.jpg`). No quieres descomprimir los 50GB.

Simplemente añade el nombre del archivo al final del comando de extracción.

```bash
$ tar -xvf backup.tar.gz fotos/perro.jpg
```
`tar` buscará ese archivo específico, lo extraerá y se detendrá.

@section: 9. Laboratorio Práctico: Operación Rescate

Vamos a practicar en la terminal.

1.  **Preparación:**
    Ve a `/tmp` y crea un entorno de prueba.
    ```bash
    $ cd /tmp
    $ mkdir lab_tar
    $ cd lab_tar
    $ mkdir documentos
    $ touch documentos/info{1..100}.txt
    ```
    (Hemos creado 100 archivos de texto).

2.  **Archivado:**
    Crea un tar simple (sin comprimir) para ver lo que ocupan.
    ```bash
    $ tar -cvf docs.tar documentos/
    $ ls -lh
    ```

3.  **Compresión Gzip:**
    Ahora con compresión.
    ```bash
    $ tar -czvf docs.tar.gz documentos/
    $ ls -lh
    ```
    (Como los archivos están vacíos, la diferencia será mínima, pero verás el archivo).

4.  **Simulacro de desastre:**
    Borra la carpeta original.
    ```bash
    $ rm -rf documentos
    ```
    ¡Oh no! Hemos perdido los datos.

5.  **Inspección:**
    Asegúrate de que el backup es bueno.
    ```bash
    $ tar -tvf docs.tar.gz
    ```

6.  **Recuperación:**
    Restaura los datos.
    ```bash
    $ tar -xvf docs.tar.gz
    $ ls -R
    ```
    ¡Tus 100 archivos han vuelto!

@section: Resumen / Cheat Sheet

| Acción | Comando | Notas |
| :--- | :--- | :--- |
| **Crear .tar.gz** | `tar -czvf archivo.tar.gz carpeta/` | Estándar, rápido. |
| **Crear .tar.xz** | `tar -cJvf archivo.tar.xz carpeta/` | Mejor compresión, lento. |
| **Extraer (Cualquiera)** | `tar -xvf archivo.tar.gz` | Detecta formato solo. |
| **Listar contenido** | `tar -tvf archivo.tar.gz` | Ver sin tocar. |
| **Extraer en carpeta** | `tar -xvf archivo.tar -C destino/` | Evita desorden. |
| **Crear ZIP** | `zip -r archivo.zip carpeta/` | Para Windows. Usa `-r`. |
| **Extraer ZIP** | `unzip archivo.zip` | |

**Reglas Mnemotécnicas:**
*   **c**rear (**c**reate)
*   e**x**traer (e**x**tract)
*   **v**erboso (**v**erbose - ver lo que pasa)
*   **z** (**gz**ip)
*   **j** (bzip2 - parece una jota)
*   **f** (**f**ile - siempre al final de los flags)

Ahora ya sabes cómo mover montañas de datos metiéndolas en bolsillos pequeños. Eres el maestro de la logística Linux.