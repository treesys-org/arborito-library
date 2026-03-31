@title: Crear, copiar, mover y borrar archivos y carpetas
@icon: 📂
@description: mkdir, touch, cp, mv, rm y enlaces; en terminal no hay papelera: implicaciones de borrar con rm.
@order: 2

# Gestión de archivos en Linux: crear, copiar, mover y borrar

Bienvenido a la clase de artes manuales digitales.

En la lección anterior aprendiste a mirar (`ls`) y a moverte (`cd`). Eras un turista pasivo.
Hoy te vas a convertir en un **Operador Activo**. Vamos a aprender a alterar la realidad del disco duro. Vamos a crear mundos (directorios), clonar entidades (copiar) y, lo más importante y peligroso, destruir información para siempre (borrar).

> **ADVERTENCIA PARA USUARIOS DE WINDOWS:**
> En Windows, si borras un archivo, va a la "Papelera de Reciclaje". Puedes recuperarlo.
> En la terminal de Linux, **NO EXISTE LA PAPELERA**.
> Cuando ejecutas el comando de borrar, el sistema no te pregunta "¿Estás seguro?". Simplemente borra. Los datos se marcan como espacio libre y se sobrescriben en milisegundos.
> **Lo que borras en la terminal, muere para siempre.**
> Lee esta guía con atención antes de usar el comando `rm`.

@section: 1. `touch`: El Creador Sutil

Antes de organizar archivos, necesitamos tener archivos.
A menudo, querrás crear un archivo vacío simplemente para probar algo, o para que un programa tenga un lugar donde escribir logs.

El comando `touch` tiene dos usos:
1.  Si el archivo **NO existe**: Crea un archivo vacío (0 bytes).
2.  Si el archivo **SÍ existe**: Actualiza su "fecha de modificación" al momento actual (como si lo hubieras tocado), pero no cambia el contenido.

### Uso Básico
```bash
# Crear un archivo vacío
$ touch carta.txt

# Crear varios a la vez
$ touch archivo1.txt archivo2.txt archivo3.txt
```

**Truco Pro (Expansión de llaves):**
Si quieres crear 100 archivos para una prueba, no escribas los 100 nombres. Usa las llaves `{}`.
```bash
$ touch foto_{1..100}.jpg
```
*¡Boom! Acabas de crear foto_1.jpg, foto_2.jpg... hasta foto_100.jpg en un milisegundo.*

@section: 2. `mkdir`: Construyendo Estructuras

`mkdir` significa **Make Directory** (Hacer Directorio). Es el equivalente a "Clic derecho -> Nueva Carpeta".

### Uso Básico
```bash
$ mkdir Mis_Documentos
```

### El Problema de los Padres Ausentes
Imagina que quieres crear una estructura para organizar tus fotos por año y mes: `Fotos/2024/Enero`.
Si intentas hacerlo directamente:
```bash
$ mkdir Fotos/2024/Enero
mkdir: cannot create directory ‘Fotos/2024/Enero’: No such file or directory
```
**¿Por qué falla?**
Porque la carpeta `Fotos` no existe. Y la carpeta `2024` tampoco. Linux se niega a crear la carpeta "nieta" (`Enero`) si no existen la "madre" y la "abuela".

### La Solución: `mkdir -p` (Parents)
La opción `-p` es una de las más útiles. Le dice a `mkdir`: *"Crea el directorio que te pido, y si los padres no existen, créalos también por el camino. Y si ya existen, no te quejes"*.

```bash
# Crea toda la jerarquía de una sola vez
$ mkdir -p Fotos/2024/Enero
```

**Truco Pro (Estructuras Complejas):**
Puedes combinar `-p` con las llaves `{}` para crear estructuras complejas en un solo comando.
```bash
# Esto crea las carpetas 2023, 2024 y 2025, y dentro de CADA UNA, crea Enero, Febrero y Marzo.
$ mkdir -p Proyecto/{2023,2024,2025}/{Ene,Feb,Mar}
```
Haz un `ls -R` (recursivo) después de eso y maravíllate.

@quiz: Quieres crear la ruta de directorios `juegos/rpg/final_fantasy` pero la carpeta `juegos` aún no existe. ¿Qué comando es el correcto?
@option: mkdir juegos/rpg/final_fantasy
@correct: mkdir -p juegos/rpg/final_fantasy
@option: mkdir -r juegos/rpg/final_fantasy
@option: touch juegos/rpg/final_fantasy

@section: 3. `cp`: El Clonador

`cp` significa **Copy**. Su misión es duplicar datos.
Sintaxis fundamental:
`cp [ORIGEN] [DESTINO]`

Piensa siempre: *"Copiar ESTO -> AQUÍ"*.

### Copiando Archivos
```bash
# Hacer una copia de seguridad de un archivo en la misma carpeta
$ cp informe.txt informe.txt.bak

# Copiar un archivo a otra carpeta (manteniendo el nombre)
$ cp foto.jpg /home/juan/Imágenes/

# Copiar un archivo a otra carpeta Y cambiarle el nombre
$ cp foto.jpg /home/juan/Imágenes/foto_vacaciones.jpg
```

### El Problema de las Carpetas: El Flag `-r`
Si intentas copiar una carpeta, `cp` te gritará.
```bash
$ cp Mis_Documentos /tmp
cp: -r not specified; omitting directory 'Mis_Documentos'
```
**¿Por qué?**
Para Linux, una carpeta no es un objeto sólido. Es una lista de archivos. Copiar la "carpeta" técnicamente solo copiaría la lista, pero no el contenido.
Necesitas decirle que copie **Recursivamente** (`-r` o `-R`). Recursivo significa: *"Copia la carpeta, entra en ella, copia lo que haya, si hay más carpetas entra en ellas... hasta el final"*.

```bash
# Forma correcta de copiar directorios
$ cp -r Mis_Documentos /tmp/
```

### Banderas Útiles para `cp`
*   `-v` (**Verbose**): Te cuenta lo que está haciendo. Útil si copias 1000 archivos y quieres ver el progreso.
    `cp -rv Fotos /media/usb/`
*   `-i` (**Interactive**): Te pregunta antes de sobrescribir algo. Vital para novatos.
    `cp -i importante.txt destino/` -> *"cp: overwrite 'destino/importante.txt'?"*
*   `-u` (**Update**): Solo copia si el archivo de origen es **más nuevo** que el de destino, o si el destino no existe. Ideal para hacer backups rápidos sin copiar todo de nuevo.

@section: 4. `mv`: El Camaleón (Mover y Renombrar)

Este comando confunde a la gente porque hace dos cosas que parecen diferentes, pero que para el disco duro son la misma operación.

`mv` significa **Move**.
Sintaxis: `mv [ORIGEN] [DESTINO]`

### Caso A: Mover de verdad
Si el destino es una **carpeta**, el archivo se mueve dentro.
```bash
$ mv carta.txt Documentos/
# Ahora 'carta.txt' ya no está aquí, está dentro de 'Documentos'.
```

### Caso B: Renombrar
Si el destino es un **nombre de archivo** (y estás en la misma carpeta), lo que haces es cambiarle el nombre.
```bash
$ mv foto_fea.jpg foto_bonita.jpg
```
**¿Por qué es lo mismo?**
Imagina que el archivo es una persona y la ruta es su dirección.
*   Mover: Cambias "Calle A, Nº 1" por "Calle B, Nº 1".
*   Renombrar: Cambias "Calle A, Nº 1" por "Calle A, Nº 2".
En ambos casos, solo estás cambiando la "dirección" o etiqueta del archivo en el índice del disco duro. Los datos físicos no se mueven (a menos que muevas entre dos discos duros diferentes).

### Mover vs. Copiar (Velocidad)
*   **En el mismo disco:** `mv` es instantáneo. Mover un archivo de 100GB tarda 0.1 segundos. Solo cambia un puntero.
*   **Entre discos distintos:** `mv` tarda mucho. Tiene que leer los 100GB del disco A, escribirlos en el disco B y luego borrar el original del disco A.

**Peligro de Sobrescritura:**
`mv` es silencioso. Si renombras `archivo1` a `archivo2`, y `archivo2` YA EXISTÍA... **`mv` destruirá el archivo2 original sin avisar** y pondrá el nuevo encima.
Para evitar infartos, usa `mv -i` (interactivo) si no estás seguro.

@quiz: Ejecutas `mv tesis_final.pdf tesis_borrador.pdf`. ¿Qué acaba de pasar?
@option: Has creado una copia llamada tesis_borrador.pdf.
@option: Has movido el archivo a la carpeta tesis_borrador.
@correct: Has renombrado el archivo. El nombre original ha desaparecido.
@option: Has borrado el archivo.

@section: 5. `rm`: El Destructor de Mundos

Llegamos al comando más infame de Unix. `rm` (**Remove**).

Como dijimos al principio: **No hay deshacer.**
Cuando escribes `rm`, el sistema operativo corta el cable que sujeta al archivo. Los datos siguen magnéticamente en el disco un rato, pero el sistema ya considera ese espacio "vacío" y listo para ser sobrescrito por tu próxima descarga de Netflix.

### Borrado Básico
```bash
$ rm archivo_inutil.txt
```
Si el archivo está protegido contra escritura, `rm` te preguntará: *"remove write-protected regular file 'archivo'?"*. Respondes `y` (yes) o `n` (no).

### Borrado de Directorios
Al igual que `cp`, `rm` no borra carpetas por defecto.
```bash
$ rm Carpeta
rm: cannot remove 'Carpeta': Is a directory
```
Para borrar una carpeta y TODO lo que hay dentro, necesitas la opción recursiva `-r`.
```bash
$ rm -r Carpeta
```
*(El sistema te pedirá confirmación por cada archivo protegido dentro).*

### El Modo Dios: `-f` (Force)
A veces tienes permisos, pero el sistema te pregunta por cada archivo. Si quieres borrar una carpeta con 10,000 archivos, no vas a pulsar "y" 10,000 veces.
La opción `-f` significa **Force** (Forzar).
*   No preguntes.
*   No te quejes si el archivo no existe.
*   Borra sin piedad.

### La Combinación Nuclear: `rm -rf`
Si combinas Recursivo (`-r`) y Forzar (`-f`), obtienes el arma definitiva.
```bash
$ rm -rf Proyecto_Antiguo/
```
Este comando borra la carpeta, todas las subcarpetas, y todos los archivos, sin preguntar nada, instantáneamente.

> **LA LEYENDA URBANA (QUE ES REAL):**
> Un error tipográfico en este comando puede destruir tu sistema.
> Si por error escribes (siendo administrador):
> `rm -rf /`
> Estás diciendo: "Borra forzosamente y recursivamente todo lo que hay desde la Raíz".
> Tu ordenador empezará a comerse a sí mismo. Primero borrará tus documentos, luego los programas, y finalmente el propio sistema operativo hasta que la pantalla se apague.
> **Regla:** Mira tres veces la pantalla antes de pulsar Enter en un `rm -rf`.

### Medidas de Seguridad para Novatos
Puedes protegerte a ti mismo usando el modo interactivo `-i`.
```bash
$ rm -i archivo.txt
rm: remove regular file 'archivo.txt'?
```
Muchos usuarios crean un "alias" (un atajo) en su configuración para que `rm` siempre sea `rm -i` por defecto.

@section: 6. `file`: El Detective

En Windows, sabes qué es un archivo por su extensión (`.exe`, `.jpg`, `.docx`). Si le cambias el nombre a `foto.jpg` por `foto.txt`, Windows intenta abrirla con el Bloc de Notas y falla.

En Linux, **las extensiones son opcionales**. A Linux le da igual cómo se llame el archivo. Le importa lo que hay dentro (los "Magic Numbers" o cabeceras binarias).

¿Tienes un archivo misterioso llamado `datos` sin extensión? Usa el comando `file`.

```bash
$ file datos
datos: PNG image data, 800 x 600, 8-bit/color RGBA, non-interlaced
```
¡Ajá! Es una imagen.

```bash
$ file script
script: Python script, ASCII text executable
```
¡Es un programa en Python!

El comando `file` es tu mejor amigo cuando descargas cosas de internet y no sabes qué son.

@section: 7. Laboratorio Práctico: El Ciclo de Vida

Vamos a cimentar estos conocimientos. Abre tu terminal y sigue estos pasos. Intenta predecir qué pasará.

1.  **Preparar el terreno:**
    Ve a tu carpeta temporal (que se borra al reiniciar, así que es segura para jugar).
    ```bash
    $ cd /tmp
    ```

2.  **Crear una estructura:**
    Crea una carpeta para el experimento.
    ```bash
    $ mkdir Lab_Archivos
    $ cd Lab_Archivos
    ```

3.  **Creación masiva:**
    Vamos a crear una estructura de carpetas y archivos vacíos.
    ```bash
    $ mkdir -p Documentos/{Trabajo,Personal}
    $ touch Documentos/Trabajo/informe_{1..5}.txt
    $ touch Documentos/Personal/foto_gato.jpg
    ```
    *Usa `tree` (si lo tienes instalado) o `ls -R` para ver lo que has creado.*

4.  **Copiar datos:**
    Vamos a hacer un backup de la carpeta Trabajo.
    ```bash
    $ cp -r Documentos/Trabajo Documentos/Trabajo_Backup
    ```

5.  **Mover y Renombrar:**
    El informe 1 está mal. Vamos a moverlo a una carpeta de "Papelera" manual que crearemos.
    ```bash
    $ mkdir Basura
    $ mv Documentos/Trabajo/informe_1.txt Basura/
    ```
    Y vamos a renombrar la foto del gato.
    ```bash
    $ mv Documentos/Personal/foto_gato.jpg Documentos/Personal/Michi.jpg
    ```

6.  **Destrucción:**
    Vamos a borrar el backup.
    ```bash
    $ rm -rf Documentos/Trabajo_Backup
    ```
    Y ahora vamos a borrar todo el laboratorio.
    ```bash
    $ cd ..  (¡Importante salir antes de borrar la carpeta donde estás!)
    $ rm -rf Lab_Archivos
    ```

Si has completado esto sin errores, ya sabes gestionar archivos mejor que el 90% de los usuarios de ordenador.

@section: Resumen / Cheat Sheet

| Comando | Acción | Truco Clave |
| :--- | :--- | :--- |
| `touch [fichero]` | Crea archivo vacío o actualiza fecha | `touch {1..10}.txt` para crear muchos. |
| `mkdir [dir]` | Crea directorio | Usa `-p` para crear árbol completo (padres). |
| `cp [ori] [des]` | Copia archivos | Usa `-r` para copiar carpetas. |
| `mv [ori] [des]` | Mueve o Renombra | Es lo mismo para el sistema. |
| `rm [fichero]` | Borra archivos | **NO HAY PAPELERA**. Es irreversible. |
| `rm -r [dir]` | Borra directorios | Necesario para borrar carpetas. |
| `rm -rf [dir]` | Borra todo a la fuerza | **PELIGROSO**. Úsalo con cuidado extremo. |
| `file [fichero]` | Dice qué tipo de archivo es | Ignora la extensión, mira el contenido. |

Recuerda: Un gran poder conlleva una gran responsabilidad. Especialmente con `rm`.