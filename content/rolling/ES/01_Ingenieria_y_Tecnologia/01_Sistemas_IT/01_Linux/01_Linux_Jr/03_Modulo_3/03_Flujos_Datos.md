@title: stdin, stdout, stderr: tuberías y redirecciones
@icon: 🚿
@description: Encadenar comandos con tuberías; redirigir entrada y salida; combinar filtros en la shell (LPIC-1).
@order: 3

# Flujos de datos en la shell: redirecciones y tuberías

Bienvenido a la lección que cambiará para siempre tu forma de ver la informática.

Hasta ahora, has usado los comandos de forma aislada. Has usado `ls` para ver archivos. Has usado `cd` para moverte. Has usado `cat` para leer.
Son herramientas útiles, pero son herramientas **aisladas**. Es como tener un martillo, una sierra y un destornillador, pero usarlos por separado.

La verdadera magia de Linux, la razón por la que domina el mundo de los servidores y la supercomputación, reside en una idea simple pero revolucionaria nacida en los años 70:

> **"Escribe programas que hagan una sola cosa y la hagan bien. Escribe programas que trabajen juntos. Escribe programas que manejen flujos de texto, porque esa es una interfaz universal."**
> — *Doug McIlroy (Inventor de las Tuberías Unix)*

En Linux, los comandos no son islas solitarias. Son piezas de LEGO. Puedes conectar la salida de un comando a la entrada de otro. Puedes redirigir lo que un programa "dice" hacia un archivo. Puedes encadenar 20 comandos para procesar terabytes de datos en segundos.

En esta lección masiva, vamos a convertirnos en **Fontaneros Digitales**. Vamos a aprender a conectar tuberías, a desviar caudales de datos y a filtrar la información como si fuera agua.

Prepárate. Vamos a profundizar en las entrañas del sistema.

@section: 1. Los Tres Canales Sagrados (Standard Streams)

Para entender cómo conectar programas, primero tienes que entender cómo se comunican.
Imagina que cada programa en Linux (como `ls`, `cat`, `grep`) es una pequeña máquina industrial o un robot.

Por defecto, cada uno de estos robots nace con **tres mangueras** conectadas a él. Ni una más, ni una menos. Estas mangueras se llaman **Flujos Estándar (Standard Streams)** y tienen números asignados por el Kernel.

### 1.1 STDIN (Standard Input) - Canal 0
*   **La Oreja del Programa.**
*   Es por donde el programa recibe información.
*   **Por defecto:** Está conectada a tu **Teclado**.
*   Cuando escribes en la terminal, estás enviando datos por el STDIN del programa que se está ejecutando.

### 1.2 STDOUT (Standard Output) - Canal 1
*   **La Boca del Programa.**
*   Es por donde el programa "habla" o escupe sus resultados normales.
*   **Por defecto:** Está conectada a tu **Pantalla** (Terminal).
*   Cuando `ls` muestra la lista de archivos, está enviando texto por su STDOUT hacia tu monitor.

### 1.3 STDERR (Standard Error) - Canal 2
*   **La Boca de Emergencia.**
*   Es un canal separado y especial reservado exclusivamente para gritar **errores**.
*   **Por defecto:** También está conectada a tu **Pantalla**.
*   *¿Por qué está separado del STDOUT?* Esto es vital. Si estás guardando los resultados de un programa en un archivo, no quieres que los mensajes de error se mezclen con tus datos válidos. Quieres ver los errores en la pantalla mientras los datos se guardan silenciosamente. Linux separa el "trigo" (STDOUT) de la "paja" (STDERR).

**Resumen Visual:**
```text
      TECLADO (Input)
         |
         v
      [ STDIN (0) ]
         |
    +-----------+
    | PROCESO   |
    +-----------+
      |       |
      |       +-----> [ STDERR (2) ] ----> PANTALLA (Errores)
      |
      +-------------> [ STDOUT (1) ] ----> PANTALLA (Datos)
```

Nuestra misión como administradores es desconectar estas mangueras de sus lugares por defecto (teclado/pantalla) y conectarlas a donde nosotros queramos (archivos u otros programas). Eso es **Redirigir**.

@section: 2. Redirección de Salida: Guardando el Flujo

Imagina que tienes un comando que genera mucha información, por ejemplo `ls -R /` (listar todos los archivos del ordenador). Si lo ejecutas, tu pantalla se llenará de texto y lo perderás.
Quieres guardar ese texto en un archivo para leerlo luego.

### 2.1 El Operador Mayor Que (`>`) - Sobrescritura
El símbolo `>` desconecta la manguera STDOUT de la pantalla y la enchufa a un archivo.

**Sintaxis:**
`comando > archivo`

**Ejemplo:**
```bash
$ echo "Hola mundo" > saludo.txt
```
Si haces esto:
1.  `echo` genera "Hola mundo".
2.  En lugar de salir por pantalla, va a `saludo.txt`.
3.  Si haces `cat saludo.txt`, verás el texto.

**¡PELIGRO DE DESTRUCCIÓN!**
El operador `>` es destructivo.
Si `saludo.txt` ya existía y tenía la novela de tu vida escrita dentro, **será borrado instantáneamente** y reemplazado por "Hola mundo". No hay "deshacer". Ten mucho cuidado.

### 2.2 El Operador Doble Mayor Que (`>>`) - Añadir (Append)
Si quieres agregar información al final de un archivo sin borrar lo que ya tenía, usas `>>`.

**Sintaxis:**
`comando >> archivo`

**Ejemplo:**
```bash
$ echo "Primera línea" > diario.txt
$ echo "Segunda línea" >> diario.txt
$ echo "Tercera línea" >> diario.txt
```
Si ahora lees el archivo (`cat diario.txt`), verás las tres líneas.
El operador `>>` es seguro. Si el archivo no existe, lo crea. Si existe, escribe al final.

**Caso de Uso Real:**
Crear un log simple de cuándo se ejecuta un script.
```bash
$ date >> registro_backups.log
```

@section: 3. Redirección de Entrada: Alimentando al Robot

Podemos hacer lo contrario: desconectar el teclado y decirle a un programa que lea sus datos desde un archivo.
Usamos el operador Menor Que (`<`).

**Sintaxis:**
`comando < archivo`

Muchos comandos aceptan nombres de archivo como argumentos (ej: `cat archivo.txt`), pero algunos programas estrictos solo saben leer desde STDIN.

**Ejemplo Clásico: `wc` (Word Count)**
El comando `wc` cuenta líneas, palabras y caracteres.
```bash
$ wc -l < diario.txt
3
```
Aquí, `wc` no sabe que existe un archivo llamado `diario.txt`. Solo sabe que le está llegando texto por su manguera de entrada (STDIN). Lo cuenta y escupe el resultado.

**Ejemplo Avanzado: Bases de Datos**
Cuando quieres restaurar una base de datos MySQL, usas esto:
```bash
$ mysql -u usuario -p base_de_datos < copia_seguridad.sql
```
Estás "inyectando" el contenido del archivo `.sql` directamente en el motor de la base de datos.

@section: 4. El Misterio del Canal 2 (STDERR)

Aquí es donde el 90% de los novatos se confunden.
Hagamos un experimento. Intenta listar un archivo que existe y uno que no existe.

```bash
$ ls existe.txt no_existe.txt > salida.txt
ls: cannot access 'no_existe.txt': No such file or directory
```

**¿Qué ha pasado?**
1.  Has redirigido la salida (`>`) al archivo `salida.txt`.
2.  Sin embargo, el mensaje de error (`ls: cannot access...`) **ha aparecido en tu pantalla**.
3.  Si miras el archivo (`cat salida.txt`), verás la información de `existe.txt`, pero NO verás el error.

**Explicación:**
El operador `>` solo redirige el **Canal 1 (STDOUT)**.
El mensaje de error viaja por el **Canal 2 (STDERR)**, que sigue conectado a tu pantalla. Las mangueras están separadas.

### 4.1 Redirigiendo Errores (`2>`)
Si quieres capturar los errores en un archivo, tienes que especificar el número del canal:

```bash
$ ls existe.txt no_existe.txt 2> errores.log
```
Ahora:
*   La información válida de `existe.txt` saldrá por pantalla (porque no redirigiste el canal 1).
*   El mensaje de error se guardará en `errores.log` y no saldrá por pantalla.

### 4.2 El Agujero Negro (`/dev/null`)
A veces, los errores no te importan. Estás ejecutando un script y no quieres que la pantalla se llene de advertencias molestas. Quieres silenciarlos.
Linux tiene un dispositivo especial llamado `/dev/null`. Es un **agujero negro digital**. Todo lo que envías allí desaparece para siempre.

```bash
# Buscar archivos ignorando los errores de "Permiso denegado"
$ find / -name "secreto" 2> /dev/null
```
Este es un truco indispensable para un administrador. Sin el `2> /dev/null`, el comando `find /` llenaría tu pantalla de basura porque intentaría entrar en carpetas del sistema donde no tienes permiso. Al enviarlos al agujero negro, solo ves los resultados útiles.

### 4.3 Redirigiendo TODO (`&>` o `2>&1`)
A veces quieres que tanto lo bueno (STDOUT) como lo malo (STDERR) vayan al mismo archivo (por ejemplo, en un log completo de instalación).

**Forma moderna (Bash):**
```bash
$ comando &> todo.log
```
El `&` significa "ambos canales".

**Forma clásica (Compatible con todo):**
```bash
$ comando > todo.log 2>&1
```
Esto parece jeroglífico, pero leámoslo despacio:
1.  `> todo.log`: "Conecta el canal 1 (Salida) al archivo".
2.  `2>&1`: "Conecta el canal 2 (Errores) a donde esté conectado el canal 1 (&1)".
Es como empalmar la manguera de errores dentro de la manguera de salida.

@quiz: Estás ejecutando un comando de copia de seguridad automático (`backup.sh`) y quieres guardar un registro de lo que ocurrió, pero quieres que los errores se guarden en un archivo separado `errores.txt`. ¿Qué sintaxis usas?
@option: ./backup.sh > log.txt > errores.txt
@correct: ./backup.sh > log.txt 2> errores.txt
@option: ./backup.sh &> log.txt
@option: ./backup.sh | errores.txt

@section: 5. La Tubería (The Pipe `|`)

Llegamos a la joya de la corona. El invento que hizo grande a Unix.
La barra vertical `|` (Pipe) nos permite conectar la **Salida (STDOUT)** de un comando directamente a la **Entrada (STDIN)** del siguiente comando.

Sin archivos intermedios. Sin guardar nada en disco. Los datos fluyen en memoria de un programa a otro como el agua por una tubería.

**Analogía del Fontanero:**
*   Comando 1: Una bomba de agua que saca agua de un pozo.
*   Comando 2: Un filtro de carbono que limpia impurezas.
*   Comando 3: Una botella donde guardas el agua.

Sin tuberías (método torpe):
1.  La bomba llena un cubo.
2.  Llevas el cubo al filtro y lo viertes.
3.  El filtro llena otro cubo.
4.  Viertes ese cubo en la botella.

Con tuberías (método Linux):
Conectas la Bomba -> Filtro -> Botella. El agua fluye continuamente.

**Sintaxis:**
`comando1 | comando2 | comando3`

### Ejemplo Básico
Queremos saber cuántos archivos hay en `/etc`.
1.  `ls /etc`: Lista los archivos (genera líneas de texto).
2.  `wc -l`: Cuenta líneas de texto que le llegan.

```bash
$ ls /etc | wc -l
245
```
`ls` no escribió en pantalla. Le pasó su lista invisiblemente a `wc`, y `wc` contó las líneas y nos mostró el número final.

### La Filosofía de los Filtros
Para que las tuberías sean útiles, necesitamos comandos que actúen como **Filtros**. Programas diseñados para recibir texto, modificarlo, y escupirlo modificado.

Vamos a ver los filtros más poderosos.

@section: 6. El Arsenal de Filtros

Estos son los comandos que vivirán a la derecha de tus tuberías. Aprenderlos es vital.

### 6.1 `grep` (El Buscador)
Ya lo vimos, pero en tuberías es el rey. Filtra líneas. Deja pasar solo las que contienen lo que buscas.

```bash
# Listar procesos -> Filtrar solo los de python
$ ps aux | grep python
```

### 6.2 `sort` (El Ordenador)
Ordena alfabéticamente o numéricamente las líneas que recibe.

```bash
# Listar usuarios, ordenarlos alfabéticamente
$ cut -d: -f1 /etc/passwd | sort
```

Opciones útiles:
*   `-n`: Orden numérico (para que 10 vaya después de 2, y no antes).
*   `-r`: Reverse (al revés).
*   `-k`: Key (ordenar por una columna específica).

### 6.3 `uniq` (El Desduplicador)
Elimina líneas duplicadas **consecutivas**.
**¡OJO!** Solo funciona si las líneas duplicadas están juntas. Por eso **siempre** se usa después de `sort`.

```bash
# Mal (no funcionará bien si los duplicados están separados)
$ comando | uniq

# Bien (primero ordenas para juntar iguales, luego quitas duplicados)
$ comando | sort | uniq
```
Opción útil: `uniq -c` (Count). Te dice cuántas veces aparecía cada línea.

### 6.4 `head` y `tail` (El Recorte)
Toman solo el principio o el final del flujo.

```bash
# Ver los 5 archivos más grandes de una carpeta
# ls -lS (lista ordenada por tamaño) -> head -n 6 (cabecera + top 5)
$ ls -lS | head -n 6
```

### 6.5 `tr` (Translate / Transform)
Sirve para sustituir caracteres o borrarlos. Muy útil para limpieza rápida.

```bash
# Convertir todo a mayúsculas
$ echo "hola mundo" | tr 'a-z' 'A-Z'
HOLA MUNDO

# Borrar espacios en blanco
$ echo "hola   mundo" | tr -d ' '
holamundo
```

### 6.6 `cut` (El Cortador Vertical)
Imagina que tienes una tabla de datos. `cut` sirve para extraer una **columna** específica.
Funciona definiendo un "delimitador" (qué carácter separa las columnas).

Ejemplo: `/etc/passwd` usa `:` como separador. El usuario es la columna 1.
```bash
$ cat /etc/passwd | cut -d: -f1
```
*   `-d:` Delimiter (dos puntos).
*   `-f1`: Field 1 (Campo 1).

### 6.7 `tee` (La T de Fontanería)
A veces quieres guardar el flujo en un archivo, pero **también** quieres verlo en pantalla para seguir procesándolo o supervisarlo.
La redirección `>` es ciega (lo guarda y no ves nada).
`tee` es una pieza en forma de T. Envía el flujo a un archivo Y lo deja pasar hacia la pantalla (o hacia otra tubería).

```bash
# Listar, guardar en archivo, y contar líneas a la vez
$ ls -l | tee lista.txt | wc -l
```
Aquí pasan dos cosas:
1.  Se crea `lista.txt` con el contenido del `ls`.
2.  Vemos en pantalla el número de líneas.

@section: 7. `xargs`: El Puente Mágico

A veces, quieres usar el resultado de un comando como **argumentos** para otro comando, no como texto de entrada.
Esto es complejo de entender al principio.

Ejemplo: `find` te devuelve una lista de nombres de archivo. Quieres borrar esos archivos con `rm`.
*   `find . -name "*.tmp" | rm` -> **NO FUNCIONA.**
    *   ¿Por qué? `rm` no lee nombres de archivo desde su STDIN. `rm` espera los nombres como argumentos (`rm archivo1 archivo2`). `rm` ignora el texto que le llega por la tubería.

Necesitamos a **`xargs`**.
`xargs` toma el texto que le llega por la tubería y lo convierte en argumentos para el comando siguiente.

```bash
# Correcto
$ find . -name "*.tmp" | xargs rm
```
Lo que hace `xargs` es construir el comando: `rm archivo1.tmp archivo2.tmp archivo3.tmp ...`

**Peligro con espacios:**
Si los archivos tienen espacios en el nombre ("mi archivo.tmp"), `xargs` fallará porque pensará que son dos archivos ("mi" y "archivo.tmp").
Para arreglarlo, usamos la opción "print0":
```bash
$ find . -name "*.tmp" -print0 | xargs -0 rm
```
Esto usa un carácter nulo invisible para separar archivos, haciendo el proceso a prueba de balas.

@section: 8. Laboratorio Práctico: Análisis de Logs

Vamos a resolver un caso real de análisis forense usando tuberías.
Imagina que tienes un servidor web y quieres saber qué IPs te están atacando.

**Escenario:** Tienes un archivo `access.log` con miles de líneas como esta:
`192.168.1.50 - - [20/Oct/2023...] "GET /index.html..." 200 ...`

**Objetivo:** Obtener el "Top 5" de direcciones IP que más visitan tu web.

**Paso 1: Extraer las IPs**
Las IPs son la primera columna. Están separadas por espacios.
```bash
$ cat access.log | cut -d' ' -f1
```
*Resultado:* Una lista larguísima de IPs desordenadas.

**Paso 2: Ordenar**
Para contar duplicados, primero hay que ordenar.
```bash
$ cat access.log | cut -d' ' -f1 | sort
```
*Resultado:* Las IPs iguales ahora están juntas.

**Paso 3: Contar ocurrencias**
Usamos `uniq -c` para contar cuántas veces aparece cada una.
```bash
$ cat access.log | cut -d' ' -f1 | sort | uniq -c
```
*Resultado:* Líneas como `  45 192.168.1.50` (45 visitas de esa IP).

**Paso 4: Ordenar por cantidad**
Ahora queremos las que tienen el número más alto. Volvemos a ordenar (`sort`), pero numéricamente (`-n`) y al revés (`-r`) para que los mayores salgan primero.
```bash
$ cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr
```

**Paso 5: El Top 5**
Cortamos solo las 5 primeras líneas.
```bash
$ cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr | head -n 5
```

¡Hemos construido una herramienta de análisis estadístico compleja en una sola línea de comando! Esto es el poder de Linux.

@quiz: ¿Qué hace exactamente el comando `sort | uniq -c`?
@option: Elimina todos los archivos duplicados del disco duro.
@option: Ordena las líneas y elimina las que son idénticas sin contarlas.
@correct: Ordena las líneas para agruparlas y luego cuenta cuántas veces aparece cada una, eliminando la redundancia visual.
@option: Cuenta las palabras únicas de un texto.

@section: 9. Resumen / Cheat Sheet

| Símbolo | Nombre | Acción |
| :--- | :--- | :--- |
| `>` | Redirección Salida | Envía STDOUT a archivo (Sobrescribe). |
| `>>` | Append Salida | Envía STDOUT a archivo (Añade al final). |
| `2>` | Redirección Error | Envía STDERR a archivo. |
| `&>` | Redirección Total | Envía STDOUT y STDERR a archivo. |
| `<` | Redirección Entrada | Lee de un archivo hacia STDIN. |
| `|` | Pipe (Tubería) | Conecta Salida de A con Entrada de B. |
| `/dev/null`| Agujero Negro | Descarta cualquier dato enviado aquí. |

**Comandos Filtro Esenciales:**
*   `grep`: Busca texto.
*   `sort`: Ordena líneas.
*   `uniq`: Quita/cuenta duplicados.
*   `cut`: Extrae columnas.
*   `wc`: Cuenta cosas (líneas, palabras).
*   `tee`: Guarda en archivo y muestra en pantalla a la vez.
*   `tr`: Transforma caracteres.
*   `xargs`: Convierte flujo en argumentos.

Ahora posees la habilidad de manipular datos como un alquimista. No necesitas abrir Excel para procesar logs. No necesitas programas caros. Tienes la terminal.