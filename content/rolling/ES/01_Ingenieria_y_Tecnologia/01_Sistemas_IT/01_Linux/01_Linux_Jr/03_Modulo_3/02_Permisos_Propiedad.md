@title: Permisos y propiedad: chmod, chown, umask y bits especiales
@icon: 🔐
@description: Modelo DAC en Linux: modo octal y simbólico, propietario y grupo, umask, SUID/SGID/sticky y riesgos de permisos demasiado abiertos.
@order: 2

# Permisos y propiedad de archivos: chmod, chown y umask

Bienvenido a la lección más importante para la seguridad de tu sistema.

En Windows (especialmente en versiones domésticas), estamos acostumbrados a que "Yo soy el dueño de mi PC". Si quiero borrar un archivo, lo borro. Si quiero instalar un juego, lo instalo. Windows asume que el usuario que está sentado frente al teclado es el amo del universo.

**Linux no confía en nadie.**

Linux fue diseñado como un sistema multiusuario desde su nacimiento. Imagina un servidor de una universidad en 1980: un solo ordenador gigante, 500 estudiantes conectados a la vez.
*   ¿Qué impide que el Estudiante A lea la tesis del Estudiante B?
*   ¿Qué impide que el Estudiante C borre el trabajo del Profesor?
*   ¿Qué impide que un programa malicioso rompa el sistema operativo?

La respuesta es una: **LOS PERMISOS.**

Cada archivo, cada carpeta, cada dispositivo (recuerda, en Linux todo es un archivo) tiene un "portero de discoteca" en la entrada. Este portero tiene una lista estricta. Antes de dejarte hacer nada (`leer`, `escribir` o `ejecutar`), el portero mira tu carnet de identidad (`UID`), mira la lista, y decide si pasas o si te da una patada (`Permission Denied`).

En esta lección masiva, vamos a diseccionar este sistema. Vamos a aprender a leer la matriz de permisos (`ls -l`), a cambiar las leyes (`chmod`), a transferir propiedades (`chown`) y a entender las artes oscuras de los permisos especiales (`SUID`, `Sticky Bit`).

Prepárate. Vamos a profundizar.

@section: 1. La Matriz: Entendiendo `ls -l`

Ya usamos `ls -l` en el módulo anterior, pero lo miramos por encima. Ahora vamos a usar el microscopio.

Abre tu terminal y escribe:
```bash
$ ls -l /etc/shadow
-rw-r----- 1 root shadow 1425 oct 20 10:00 /etc/shadow
```

Centrémonos en la primera columna, esa cadena críptica de 10 caracteres:
**`-rw-r-----`**

Esta cadena es el mapa de seguridad del archivo. Se divide en 4 partes lógicas:

`[TIPO] [DUEÑO] [GRUPO] [OTROS]`

### 1.1 El Primer Carácter: El Tipo
El primer carácter nos dice **qué es** esto.
*   **`-` (Guion):** Es un archivo normal (texto, imagen, ejecutable, música).
*   **`d` (Directory):** Es una carpeta (directorio).
*   **`l` (Link):** Es un enlace simbólico (un acceso directo a otro sitio).
*   **`c` / `b`:** Dispositivos especiales (Character/Block). Los verás en `/dev`.

### 1.2 Los Tres Tríos (La Santísima Trinidad)
Los siguientes 9 caracteres se agrupan de 3 en 3.
Cada trío representa los permisos de una entidad diferente.

1.  **Carácteres 2-4 (El DUEÑO / User):** Qué puede hacer el propietario del archivo.
2.  **Carácteres 5-7 (El GRUPO / Group):** Qué puede hacer cualquier usuario que pertenezca al grupo dueño del archivo.
3.  **Carácteres 8-10 (LOS OTROS / Others):** Qué puede hacer el resto del mundo (cualquiera que no sea el dueño ni esté en el grupo).

Volvamos al ejemplo: `-rw-r-----`
1.  Tipo: `-` (Archivo).
2.  Dueño (`rw-`): Puede Leer y Escribir.
3.  Grupo (`r--`): Puede Leer.
4.  Otros (`---`): No pueden hacer NADA.

Esto significa que `root` (el dueño) puede editar el archivo. El grupo `shadow` puede leerlo (útil para verificar contraseñas). Y tú, usuario mortal, no puedes ni verlo. Es seguro.

@section: 2. R, W, X: ¿Qué significan realmente?

Aquí es donde la gente se confunde.
`r` (Read), `w` (Write), `x` (Execute).
Parece obvio, ¿verdad?
Para un **archivo**, sí lo es.
Pero para una **carpeta (directorio)**, el significado cambia sutilmente pero es vital entenderlo.

### Caso A: Permisos en ARCHIVOS (Files)

*   **`r` (Lectura):** Puedes abrir el archivo y ver su contenido (`cat`, `less`, abrir en editor).
    *   *Sin esto:* El sistema te dice "Permission denied" al intentar leerlo.
*   **`w` (Escritura):** Puedes modificar el contenido, truncarlo (vaciarlo) o escribir sobre él.
    *   *OJO:* El permiso `w` en un archivo **NO** te da permiso para borrar el archivo. Para borrar un archivo, necesitas permiso en la *carpeta* que lo contiene, no en el archivo mismo.
*   **`x` (Ejecución):** Puedes decirle al sistema que intente ejecutar este archivo como un programa o script.
    *   *Nota:* En Windows, un archivo es ejecutable si acaba en `.exe`. En Linux, la extensión da igual. Es ejecutable si tiene el bit `x`.

### Caso B: Permisos en DIRECTORIOS (Carpetas)

Aquí la lógica cambia. Una carpeta es un "contenedor".

*   **`r` (Lectura):** Puedes listar el contenido (`ls`). Puedes ver qué archivos hay dentro.
    *   *Sin esto:* `ls` te dará error.
*   **`w` (Escritura):** Puedes **crear** y **borrar** archivos dentro de esta carpeta.
    *   *Peligro:* Si tienes `w` en una carpeta, puedes borrar cualquier archivo de dentro, **incluso si ese archivo no es tuyo y no tienes permiso de escritura sobre él**. Eres el dueño del contenedor, así que puedes tirar el contenido.
*   **`x` (Ejecución / Paso):** Este es el más difícil de entender. En una carpeta, `x` significa **"Permiso de entrar"** o "Atravesar".
    *   Te permite hacer `cd` dentro de la carpeta.
    *   Te permite acceder a los metadatos de los archivos de dentro (si sabes sus nombres).
    *   *Combinación Típica:* Si tienes `r` pero no `x` en una carpeta, puedes hacer `ls` y ver los nombres de los archivos, pero no puedes hacer `cd` ni leer el tamaño o fecha de los archivos. Verás muchos signos de interrogación `????`.

**Resumen Vital:** Para usar una carpeta normalmente, necesitas **`r-x`** (ver y entrar).

@quiz: Tienes una carpeta llamada `SECRETOS` con permisos `rwxr--r--` (Dueño: rwx, Grupo: r--, Otros: r--). Eres un usuario "Otro". ¿Puedes hacer `cd SECRETOS`?
@option: Sí, porque tengo permiso de lectura (r).
@correct: No, porque necesito permiso de ejecución (x) en el directorio para poder entrar.
@option: Sí, pero no podré hacer ls.
@option: Depende del contenido de la carpeta.

@section: 3. `chmod`: El Legislador (Modo Simbólico)

El comando `chmod` (**Ch**ange **Mod**e) es la herramienta para cambiar estos permisos.
Tiene dos modos de uso. Empezaremos por el **Modo Simbólico**, que es más intuitivo para humanos (letras).

**Sintaxis:**
`chmod [QUIÉN] [OPERADOR] [PERMISO] archivo`

### Los Actores (Quién)
*   `u`: **User** (Dueño).
*   `g`: **Group** (Grupo).
*   `o`: **Others** (Otros).
*   `a`: **All** (Todos: u+g+o).

### Los Operadores
*   `+`: Añadir un permiso (deja los otros intactos).
*   `-`: Quitar un permiso.
*   `=`: Establecer exactamente estos permisos (borra los anteriores).

### Los Permisos
*   `r`, `w`, `x`.

**Ejemplos de la vida real:**

1.  **Hacer un script ejecutable:**
    Acabas de escribir `script.sh`. Quieres ejecutarlo.
    ```bash
    $ chmod u+x script.sh
    ```
    *Traducción:* "Al dueño (u), añádele (+) ejecución (x)".

2.  **Proteger un documento secreto:**
    No quieres que nadie más lo lea.
    ```bash
    $ chmod go-rwx secreto.txt
    ```
    *Traducción:* "Al grupo y a otros (go), quítales (-) lectura, escritura y ejecución".

3.  **Compartir con el grupo, bloquear al resto:**
    ```bash
    $ chmod g=r,o= archivo.txt
    ```
    *Traducción:* "Al grupo asígnale solo lectura. A otros, asígnales... nada (vacío)".

4.  **Hacer público para todos:**
    ```bash
    $ chmod a+r foto.jpg
    ```
    *Traducción:* "A todos (a), añadir lectura".

Este modo es genial para cambios rápidos ("ah, se me olvidó dar permiso de ejecución").

@section: 4. `chmod`: El Matemático (Modo Octal)

El Modo Simbólico es lento si quieres definir todos los permisos de golpe. Los administradores profesionales usan el **Modo Octal** (numérico).
Aquí es donde Linux se pone "Matrix".

A cada permiso se le asigna un valor numérico basado en bits binarios:

*   **`r` (Read) = 4**
*   **`w` (Write) = 2**
*   **`x` (Exec) = 1**
*   **`-` (Nada) = 0**

### La Suma Mágica
Para saber el permiso de un grupo, solo **SUMAS** los valores.

*   Quiero Lectura y Escritura (`rw-`): 4 + 2 = **6**.
*   Quiero Lectura y Ejecución (`r-x`): 4 + 1 = **5**.
*   Quiero Todo (`rwx`): 4 + 2 + 1 = **7**.
*   Quiero Solo Lectura (`r--`): 4.
*   No quiero nada (`---`): 0.

### El Código de Tres Dígitos
Como tenemos 3 grupos de personas (Dueño, Grupo, Otros), usamos 3 números.

**El Legendario `755`:**
Es el permiso estándar para programas y carpetas públicas.
*   Dueño (7): 4+2+1 = `rwx` (Hace lo que quiera).
*   Grupo (5): 4+0+1 = `r-x` (Lee y Ejecuta).
*   Otros (5): 4+0+1 = `r-x` (Lee y Ejecuta).
*   *Resultado:* `-rwxr-xr-x`.

**El Privado `600`:**
Ideal para archivos de texto secretos (como claves SSH).
*   Dueño (6): 4+2 = `rw-` (Lee y escribe).
*   Grupo (0): Nada.
*   Otros (0): Nada.
*   *Resultado:* `-rw-------`.

**El Pecado Capital `777`:**
*   Dueño (7): `rwx`.
*   Grupo (7): `rwx`.
*   Otros (7): `rwx`.
*   *Resultado:* `-rwxrwxrwx`.
*   *Significado:* **CUALQUIERA** en el sistema puede leer, borrar, modificar o ejecutar tu archivo.
*   *Uso:* **NUNCA**. Si un tutorial de internet te dice "haz chmod 777 para arreglar el problema", ese tutorial es basura. Estás abriendo la puerta de tu casa y quitando las cerraduras.

**Ejemplos de comandos octales:**
```bash
$ chmod 755 script.sh
$ chmod 644 documento.txt
$ chmod 700 carpeta_privada/
```

**Truco:** `-R` (Recursivo).
Para cambiar los permisos de una carpeta y *todo lo que hay dentro*:
```bash
$ chmod -R 755 /var/www/html
```

@quiz: ¿Cuál es el equivalente numérico (octal) de los permisos `rw-r--r--`?
@option: 755
@correct: 644
@option: 600
@option: 777

@section: 5. `chown` y `chgrp`: Cambiando de Dueño

A veces el problema no son los permisos, sino **quién** es el dueño.
Por defecto, cuando creas un archivo, es tuyo. Pero a veces necesitas darle ese archivo a otro usuario (o a `root`, o a un servicio como `www-data`).

### El Comando `chown` (Change Owner)
Solo `root` (o alguien con `sudo`) puede regalar archivos a otros. Tú no puedes "regalar" tu archivo a otro usuario por seguridad (podrías llenar su disco duro con basura y él no podría borrarla porque es tuya... es complicado, pero confía en mí).

**Sintaxis:**
`chown [USUARIO]:[GRUPO] archivo`

**Ejemplos:**

1.  **Cambiar solo el dueño:**
    ```bash
    $ sudo chown maria informe.txt
    ```
    Ahora el archivo pertenece a María. El grupo sigue siendo el original.

2.  **Cambiar dueño y grupo a la vez (Lo más común):**
    ```bash
    $ sudo chown www-data:www-data index.html
    ```
    Esto asigna el archivo al usuario `www-data` y al grupo `www-data`.

3.  **Cambiar solo el grupo:**
    Puedes usar `chown :grupo archivo` (con los dos puntos delante) o el comando específico `chgrp`.
    ```bash
    $ sudo chown :developers codigo.py
    # O bien
    $ sudo chgrp developers codigo.py
    ```

4.  **Recursivo (¡Cuidado!):**
    ```bash
    $ sudo chown -R juan:juan /home/juan/
    ```
    Esto asegura que todo en la carpeta de Juan le pertenezca a él.

@section: 6. Las Artes Oscuras: Permisos Especiales

Hasta ahora hemos visto los 9 bits estándar. Pero existen **3 bits especiales** que otorgan superpoderes. Son herramientas avanzadas, pero necesitas reconocerlas.

Se representan con una 4ª cifra en el modo octal (ej: `4755`) o con letras especiales en el `ls -l`.

### 6.1 SUID (Set User ID) - El Manto del Rey
*   **Valor:** 4000.
*   **Símbolo:** Una `s` en lugar de la `x` del Dueño (`rwsr-xr-x`).
*   **Qué hace:** Cuando ejecutas un archivo con SUID, **se ejecuta con los permisos del DUEÑO del archivo**, no con los tuyos.

**El ejemplo clásico: `passwd`**
Tú (usuario normal) necesitas cambiar tu contraseña. Eso implica escribir en `/etc/shadow`. Pero `/etc/shadow` es propiedad de `root` y tú no tienes permiso de escritura.
¿Cómo lo haces?
El programa `/usr/bin/passwd` tiene el bit SUID activado y pertenece a `root`.
Cuando ejecutas `passwd`, el programa se "disfraza" de `root` temporalmente, edita el archivo por ti, y luego termina.
Es una elevación de privilegios controlada.

### 6.2 SGID (Set Group ID) - El Trabajo en Equipo
*   **Valor:** 2000.
*   **Símbolo:** Una `s` en la `x` del Grupo (`rwxr-sr-x`).
*   **Qué hace (en carpetas):** Cualquier archivo creado dentro de esta carpeta **heredará el GRUPO de la carpeta**, no el grupo principal del usuario que lo creó.
*   **Uso:** Colaboración. Si tienes una carpeta compartida `/srv/proyecto` del grupo `devs`, activas el SGID. Así, si Ana crea un archivo, el archivo pertenecerá al grupo `devs` (y Pedro podrá editarlo) en lugar de pertenecer al grupo `ana` (que Pedro no podría tocar).

### 6.3 Sticky Bit - La Etiqueta de Propiedad
*   **Valor:** 1000.
*   **Símbolo:** Una `t` en la `x` de Otros (`rwxrwxrwt`).
*   **Qué hace:** En una carpeta donde *todos* tienen permiso de escritura (como `/tmp`), impide que los usuarios borren archivos que **no son suyos**.
*   **Uso:** Mira `/tmp`. Todo el mundo puede tirar cosas allí. Pero tú no puedes borrar los archivos temporales de otro usuario. Solo el dueño (y root) puede borrar su propia basura.

@quiz: ¿Qué efecto tiene el 'Sticky Bit' en un directorio como `/tmp`?
@option: Hace que los archivos se borren automáticamente después de un tiempo.
@option: Permite que cualquiera borre cualquier archivo.
@correct: Impide que los usuarios borren archivos que no les pertenecen, aunque tengan permiso de escritura en la carpeta.
@option: Hace que los archivos se queden en memoria RAM.

@section: 7. El Molde por Defecto: `umask`

Cuando creas un archivo (`touch nuevo`), ¿qué permisos tiene por defecto? ¿Por qué es `644` y no `600` o `777`?

Esto lo decide la **`umask`** (User Mask).
La umask es un filtro de sustracción.
*   Los archivos nacen intentando ser `666` (rw-rw-rw-).
*   Las carpetas nacen intentando ser `777` (rwxrwxrwx).

La umask resta permisos a esos valores iniciales.
La umask típica es `0022`.
*   Archivo: 666 - 022 = **644** (rw-r--r--).
*   Carpeta: 777 - 022 = **755** (rwxr-xr-x).

Si quieres privacidad total por defecto, puedes cambiar tu umask a `0077` (resta todo a grupo y otros).
*   Archivo: 666 - 077 = **600** (rw-------). Nadie más puede leer tus nuevos archivos.

@section: 8. Laboratorio Práctico: El Incidente de Seguridad

Vamos a simular un escenario de auditoría.

1.  **Crear el escenario:**
    ```bash
    $ mkdir /tmp/lab_permisos
    $ cd /tmp/lab_permisos
    $ touch secreto.txt
    ```

2.  **Análisis:**
    Haz `ls -l`.
    Probablemente veas `-rw-r--r--`. Todo el mundo puede leer tu secreto. ¡Mal!

3.  **Blindaje:**
    Vamos a hacerlo privado.
    ```bash
    $ chmod 600 secreto.txt
    $ ls -l
    -rw------- ... secreto.txt
    ```
    Ahora solo tú puedes leerlo.

4.  **Crear un script público:**
    ```bash
    $ echo "echo Hola" > script.sh
    ```
    Intenta ejecutarlo: `./script.sh`.
    Error: `Permission denied`.
    ¿Por qué? Falta la `x`.

5.  **Hacerlo ejecutable:**
    ```bash
    $ chmod u+x script.sh
    $ ./script.sh
    Hola
    ```
    ¡Funciona!

6.  **Jugar con grupos:**
    *(Requiere sudo)*
    Vamos a regalar el secreto a root.
    ```bash
    $ sudo chown root:root secreto.txt
    ```
    Intenta leerlo: `cat secreto.txt`.
    `Permission denied`.
    ¡Ya no es tuyo! Aunque lo creaste tú, se lo has dado a root y él tiene permisos `rw-------`. Te has bloqueado a ti mismo. Así de estricto es Linux.

7.  **Limpieza:**
    ```bash
    $ cd ..
    $ rm -rf lab_permisos
    ```

@section: Resumen / Cheat Sheet

| Acción | Comando | Ejemplo |
| :--- | :--- | :--- |
| **Ver Permisos** | `ls -l` | |
| **Permisos Numéricos** | **r=4, w=2, x=1** | |
| **Hacer ejecutable** | `chmod +x` | `chmod +x run.sh` |
| **Hacer privado** | `chmod 600` | Solo yo leo/escribo. |
| **Estándar Archivo** | `chmod 644` | Yo escribo, todos leen. |
| **Estándar Carpeta** | `chmod 755` | Yo escribo, todos entran. |
| **Cambiar Dueño** | `chown usuario` | `sudo chown ana file` |
| **Cambiar Grupo** | `chown :grupo` | `sudo chown :devs file` |
| **Recursivo** | `-R` | `chmod -R 755 carpeta` |

Dominar los permisos es la diferencia entre un sistema seguro y un colador. Si alguna vez tienes un error de "Permission Denied", no corras a usar `sudo` o `chmod 777`. Párate, mira el `ls -l`, piensa quién eres tú y qué permiso te falta. **Sé el guardián, no el intruso.**
