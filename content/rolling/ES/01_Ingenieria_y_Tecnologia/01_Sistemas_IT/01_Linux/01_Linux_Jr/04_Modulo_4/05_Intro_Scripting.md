@title: Introducción a scripts Bash: variables, condicionales y bucles
@icon: 📜
@description: Primeros scripts en Bash: shebang, variables, if/for, argumentos y buenas prácticas para automatizar tareas en Linux.
@order: 5

# Introducción a scripting en Bash: automatizar tareas en la shell

Bienvenido a la lección final del curso "Linux Jr".

Hasta ahora, has usado Linux como un operador.
*   Has pedido listas de archivos (`ls`).
*   Has movido cosas (`mv`).
*   Has instalado programas (`apt`).

Has sido un usuario que da órdenes sueltas. Pero, ¿qué pasa si quieres hacer una copia de seguridad de tus fotos, comprimirla con fecha de hoy, enviarla a un servidor remoto y luego borrar los temporales, todo eso cada viernes a las 3 AM?
¿Vas a despertarte a las 3 AM para teclear los 4 comandos? **No.**

Aquí es donde entra el **Scripting**.
Un Script no es más que un archivo de texto donde escribes los comandos que quieres ejecutar, uno tras otro. Pero añade algo mágico: **Lógica**.
Puedes decir: *"Si la carpeta existe, copia esto. Si no existe, créala"*. O *"Repite esto para cada archivo que encuentres"*.

En esta guía masiva, vamos a enseñarte el lenguaje **Bash**. Es el lenguaje nativo de la terminal. No necesitas instalar Python ni C++. Ya lo tienes. Es el pegamento que mantiene unido el mundo de los servidores.

Prepárate. Hoy dejas de usar herramientas. Hoy empiezas a construirlas.

@section: 1. El Ritual de Iniciación: Shebang y Permisos

Para crear un script, solo necesitas un editor de texto (`nano`).
Vamos a crear el clásico "Hola Mundo", pero entendiendo cada átomo.

### 1.1 El Shebang (`#!`)
Abre tu terminal y crea un archivo:
```bash
$ nano hola.sh
```

Escribe esto dentro:
```bash
#!/bin/bash
echo "¡Hola, mundo de la automatización!"
```

La primera línea es sagrada. Se llama **Shebang**.
*   `#`: En Bash, la almohadilla significa "comentario" (ignorar esta línea).
*   `!`: Pero si va seguida de una exclamación, es una directiva especial para el Kernel.
*   `/bin/bash`: Le dice al sistema: *"Oye, este archivo no es un texto normal. Quiero que cargues el programa `/bin/bash` y le pases este archivo para que lo ejecute"*.

Podrías poner `#!/usr/bin/python3` y escribir en Python. Pero hoy estamos con Bash.

### 1.2 La Ejecución Fallida
Guarda (`Ctrl+O`) y sal (`Ctrl+X`).
Intenta ejecutarlo:
```bash
$ ./hola.sh
bash: ./hola.sh: Permission denied
```
**¿Por qué?**
Linux, por seguridad, **nunca** permite ejecutar un archivo de texto recién creado. Imagina que te bajas un virus en texto `virus.txt` y le haces doble clic. Si se ejecutara, sería un desastre.
Para que un archivo sea un "Programa", debes otorgarle el don de la vida (el bit de ejecución).

### 1.3 El Rito de Activación (`chmod`)
```bash
$ chmod +x hola.sh
```
*   `+x`: Add eXecutable permission.

Ahora sí:
```bash
$ ./hola.sh
¡Hola, mundo de la automatización!
```

**¿Por qué `./`?**
Si escribes solo `hola.sh`, la terminal buscará ese comando en las carpetas del sistema (`/usr/bin`, etc.) y no lo encontrará.
El punto `.` significa "Aquí mismo". Le dices: *"Ejecuta el archivo hola.sh que está en esta carpeta actual"*.

@section: 2. Variables: Cajas con Etiquetas

En álgebra, `x = 5`. En programación es igual.
Una variable es una caja con un nombre donde guardas un dato para usarlo luego.

### 2.1 Creación (Asignación)
Regla de oro: **NO PONGAS ESPACIOS ALREDEDOR DEL IGUAL.**

```bash
# MAL (Bash pensará que "NOMBRE" es un comando y "=" es un argumento)
NOMBRE = Juan

# BIEN
NOMBRE="Juan"
EDAD=25
FECHA="2025-01-01"
```

### 2.2 Uso (Interpolación)
Para ver lo que hay dentro de la caja, usas el símbolo del dólar `$`.

```bash
echo "Hola, me llamo $NOMBRE y tengo $EDAD años."
```

Si quieres ser muy preciso (o pegar la variable a otra letra), usa llaves `${}`:
```bash
ARCHIVO="foto"
echo "Voy a procesar el archivo ${ARCHIVO}_vacaciones.jpg"
# Sin llaves, buscaría la variable $ARCHIVO_vacaciones, que no existe.
```

### 2.3 Comillas: La Guerra de " vs '
Esto confunde a todos los principiantes.

*   **Comillas Dobles (`"`):** Son "blandas". Permiten que la magia ocurra dentro. Si pones una variable `$NOMBRE` dentro, Bash la sustituirá por su valor.
*   **Comillas Simples (`'`):** Son "duras" o literales. Protegen todo lo que hay dentro. Nada cambia.

**Ejemplo:**
```bash
NOMBRE="Ana"
echo "Hola $NOMBRE"  -> Imprime: Hola Ana
echo 'Hola $NOMBRE'  -> Imprime: Hola $NOMBRE
```
*Consejo:* Usa comillas dobles por defecto. Usa simples solo cuando quieras escribir símbolos raros ($ ! ` \) y no quieras que Bash los toque.

@section: 3. Interactividad: Hablando con el Humano (`read`)

Un script que solo habla es aburrido. Vamos a hacer que escuche.
El comando `read` pausa el script y espera a que el usuario escriba algo.

```bash
#!/bin/bash

echo "¿Cómo te llamas, viajero?"
read USUARIO

echo "Bienvenido, $USUARIO. Preparando los motores..."
sleep 2  # Pausa dramática de 2 segundos
echo "¡Motores listos!"
```

**Opción Pro (`-p`):**
Puedes poner la pregunta dentro del propio `read` para ahorrar líneas.
```bash
read -p "Introduce tu edad: " EDAD
echo "Tienes $EDAD años."
```

@section: 4. Argumentos: Automatización sin Preguntas

Si quieres automatizar algo que se ejecute a las 4 AM, no puede haber nadie para responder preguntas con `read`.
Necesitas pasarle los datos al arrancar el script.
Ejemplo: `./copiador.sh archivo1.txt archivo2.txt`

Bash asigna números a estas palabras automáticamente:

*   **`$0`**: El nombre del propio script (`./copiador.sh`).
*   **`$1`**: El primer argumento (`archivo1.txt`).
*   **`$2`**: El segundo argumento (`archivo2.txt`).
*   ...
*   **`$#`**: El número total de argumentos recibidos.

**Ejemplo Práctico:**
Crea `saludo_formal.sh`:
```bash
#!/bin/bash
echo "Buenos días, Sr. $1."
echo "Veo que su apellido es $2."
```

Ejecútalo:
```bash
$ ./saludo_formal.sh James Bond
Buenos días, Sr. James.
Veo que su apellido es Bond.
```

@section: 5. El Poder de la Lógica: Condicionales (`if`)

Ahora el script va a tomar decisiones. Si pasa A, haz B. Si no, haz C.

La sintaxis de Bash es un poco... peculiar. Respeta los espacios escrupulosamente.

```bash
if [ CONDICIÓN ]; then
    # Comandos si es VERDAD
else
    # Comandos si es FALSO
fi
```
*(Fíjate en que `if` termina con `fi` al revés. Humor de programador).*

### 5.1 Comparar Números
*   `-eq`: Igual (Equal).
*   `-ne`: No igual (Not Equal).
*   `-gt`: Mayor que (Greater Than).
*   `-lt`: Menor que (Less Than).
*   `-ge`: Mayor o igual.
*   `-le`: Menor o igual.

**Ejemplo:**
```bash
read -p "Dime un número: " NUMERO

if [ "$NUMERO" -gt 10 ]; then
    echo "Es un número grande."
else
    echo "Es un número pequeño."
fi
```
**IMPORTANTE:** Fíjate en los espacios dentro de los corchetes `[ ... ]`.
`["$NUMERO" -gt 10]` -> **ERROR**.
`[ "$NUMERO" -gt 10 ]` -> **BIEN**. (Espacio después de `[` y antes de `]`).

### 5.2 Comparar Texto
*   `=`: Igual.
*   `!=`: Diferente.
*   `-z`: La cadena está vacía (Zero length).

```bash
if [ "$USUARIO" = "root" ]; then
    echo "¡Cuidado! Eres el administrador."
fi
```

### 5.3 Comprobar Archivos (Lo más útil)
Esto es lo que usarás el 90% del tiempo en administración.
*   `-f archivo`: ¿Existe y es un archivo normal?
*   `-d archivo`: ¿Existe y es un directorio?
*   `-e archivo`: ¿Existe (sea lo que sea)?
*   `-r archivo`: ¿Tengo permiso de lectura?
*   `-w archivo`: ¿Tengo permiso de escritura?

**El Script "Instalador Seguro":**
```bash
CARPETA_CONFIG="/etc/mi_app"

if [ -d "$CARPETA_CONFIG" ]; then
    echo "La carpeta ya existe. No hago nada."
else
    echo "Creando carpeta..."
    mkdir "$CARPETA_CONFIG"
fi
```

@quiz: ¿Qué operador usarías dentro de un `if` para comprobar si una variable numérica `$A` es menor que `$B`?
@option: <
@option: -min
@correct: -lt
@option: <<

@section: 6. La Fuerza de la Repetición: Bucles (`for`)

Imagina que tienes que convertir 1000 imágenes de `.png` a `.jpg`. ¿Vas a escribir 1000 comandos?
Usamos un bucle `for` para decir: *"Para cada elemento de esta lista, haz esto"*.

**Sintaxis:**
```bash
for VARIABLE in LISTA; do
    # Comandos
done
```

### 6.1 Iterar sobre una lista manual
```bash
for COLOR in rojo verde azul; do
    echo "Me gusta el color $COLOR"
done
```

### 6.2 Iterar sobre archivos (Globbing)
Esto es potentísimo. Bash expande el `*.txt` antes de ejecutar el bucle.

```bash
# Script para hacer backup de todos los .txt
for ARCHIVO in *.txt; do
    echo "Copiando $ARCHIVO..."
    cp "$ARCHIVO" "$ARCHIVO.bak"
done
```

### 6.3 Rangos numéricos
```bash
# Contar del 1 al 10
for i in {1..10}; do
    echo "Cuentra atrás: $i"
    sleep 1
done
```

@section: 7. Matemáticas y Comandos dentro de Comandos

### 7.1 Matemáticas (`$(( ))`)
Bash solo maneja números enteros. Para calcular, usa doble paréntesis.

```bash
A=5
B=2
SUMA=$((A + B))
echo "La suma es $SUMA"
```

### 7.2 Sustitución de Comandos (`$( )`)
Esto es vital. Permite guardar **el resultado de un comando** dentro de una variable.
Se usa `$(comando)`.

**Ejemplo: Guardar la fecha en el nombre del archivo**
```bash
HOY=$(date +%F)
# Si hoy es 2025-01-20, la variable HOY vale "2025-01-20"

tar -czf "backup_$HOY.tar.gz" /home/usuario/documentos
```
Este script creará un archivo diferente cada día: `backup_2025-01-20.tar.gz`, `backup_2025-01-21.tar.gz`...

**Ejemplo: ¿Quién soy?**
```bash
YO=$(whoami)
echo "El script se está ejecutando como: $YO"
```

@section: 8. Exit Codes: El Semáforo del Éxito

En Linux, cuando un programa termina, deja un pequeño número secreto: el **Exit Code**.
*   **0**: Éxito. Todo fue bien.
*   **1-255**: Error. Algo falló.

Puedes ver el código del último comando ejecutado con la variable especial **`$?`**.

```bash
ls /archivo_que_no_existe
echo "El código fue: $?"
# Imprimirá: El código fue: 2 (o similar, distinto de 0)
```

**¿Para qué sirve?**
Para encadenar lógica.
"Intenta descargar el archivo. SI (&&) sale bien, descomprímelo. SI NO (||), manda un error".

### Los operadores `&&` y `||`
*   `comando1 && comando2`: Ejecuta el 2 solo si el 1 tuvo éxito (Exit Code 0).
*   `comando1 || comando2`: Ejecuta el 2 solo si el 1 FALLÓ.

**Ejemplo Pro:**
```bash
# Actualizar el sistema en una sola línea
sudo apt update && sudo apt upgrade -y
```
*(Si `update` falla porque no hay internet, `upgrade` no se ejecutará. Seguridad ante todo).*

@section: 9. Funciones: No te repitas (DRY)

Si vas a usar un trozo de código varias veces, enciérralo en una función.

```bash
function log_error {
    echo "[ERROR] - $(date) - $1" >> errores.log
    echo "¡Ha ocurrido un error! Mira el log."
}

# Uso
mkdir /root/prueba 2> /dev/null

if [ $? -ne 0 ]; then
    log_error "No pude crear la carpeta en root. Permiso denegado."
    exit 1
fi
```

@section: 10. Laboratorio Práctico Final: "El Organizador de Descargas"

Vamos a construir un script real y útil.
**Problema:** Tu carpeta de Descargas es un caos. Hay imágenes, pdfs y zips mezclados.
**Solución:** Un script que detecte los tipos de archivo y los mueva a carpetas ordenadas automáticamente.

Crea el archivo `organizador.sh` y dale permisos `+x`.

```bash
#!/bin/bash

# Directorio a organizar (Usa tu usuario real o $USER)
ORIGEN="/home/$USER/Descargas"

# Comprobamos si la carpeta existe
if [ ! -d "$ORIGEN" ]; then
    echo "Error: La carpeta $ORIGEN no existe."
    exit 1
fi

echo "Iniciando limpieza en $ORIGEN..."

# Entramos en la carpeta
cd "$ORIGEN"

# 1. Mover Imágenes
# Comprobamos si hay algún jpg o png antes de intentar mover
# (ls *.jpg devuelve error si no hay ninguno, así que redirigimos error a null)
if ls *.jpg *.png >/dev/null 2>&1; then
    echo "Detectadas imágenes. Moviendo..."
    mkdir -p Imagenes
    mv *.jpg *.png Imagenes/
fi

# 2. Mover Documentos
if ls *.pdf *.docx *.txt >/dev/null 2>&1; then
    echo "Detectados documentos. Moviendo..."
    mkdir -p Documentos
    mv *.pdf *.docx *.txt Documentos/
fi

# 3. Mover Instaladores/Comprimidos
if ls *.zip *.tar.gz *.deb >/dev/null 2>&1; then
    echo "Detectados paquetes. Moviendo..."
    mkdir -p Paquetes
    mv *.zip *.tar.gz *.deb Paquetes/
fi

echo "¡Limpieza terminada!"
ls -F
```

**Análisis del script:**
1.  Usa variables para definir rutas.
2.  Usa `if [ ! -d ... ]` para comprobar errores antes de empezar (Defensive Programming).
3.  Usa `mkdir -p` para no fallar si la carpeta ya existe.
4.  Usa `>/dev/null 2>&1` para que `ls` no ensucie la pantalla si no encuentra archivos.
5.  Organiza todo automáticamente.

@section: 11. Modo estricto para scripts serios (`set`) y tareas programadas

### `set -euo pipefail` (patrón profesional)

En scripts de producción (y en muchas plantillas de examen avanzado) se activa:

```bash
set -e          # salir si un comando falla (status != 0)
set -u          # error si usas variable no definida
set -o pipefail # el pipeline falla si falla cualquier parte (no solo el último)
```

O en una línea: `set -euo pipefail` justo después del shebang. **Ojo:** puede hacer que scripts de prueba “rompan” antes; úsalo cuando quieras **fallar rápido** y logs claros.

### `cron` y `at` (visión operativa)

*   **`crontab -e`:** edita la tabla del usuario. Formato: `minuto hora día mes día_semana comando`.
*   **`/etc/crontab` y `/etc/cron.*`:** tareas del sistema (requiere root).
*   **`at`:** una sola ejecución diferida (`at 15:00`, luego escribes comandos y `Ctrl+D`).

Ejemplo: ejecutar el organizador cada domingo a las 03:00:

```bash
0 3 * * 0 /home/usuario/bin/organizador.sh >> /var/log/organizador.log 2>&1
```

**RHEL:** `cronie` suele ser el paquete; **Debian/Ubuntu:** `cron`. Verifica con `systemctl status cron` o `crond`.

### Herramienta ShellCheck

Antes de subir un script a producción, pásalo por **ShellCheck** (`shellcheck script.sh`) para detectar comillas, variables sin comillar y errores clásicos.

@quiz: ¿Qué hace `set -u` en Bash?
@option: Desactiva los errores
@correct: Provoca error si se expande una variable no definida
@option: Activa modo gráfico

@quiz: ¿Qué archivo edita un usuario para sus tareas recurrentes en cron?
@option: /etc/fstab
@correct: crontab del usuario (`crontab -e`)
@option: /etc/hosts

@section: Resumen / Cheat Sheet

| Símbolo | Significado | Ejemplo |
| :--- | :--- | :--- |
| `#!/bin/bash` | Shebang | Primera línea obligatoria. |
| `$VAR` | Variable | `echo $NOMBRE` |
| `read` | Input usuario | `read -p "Dime algo: " VAR` |
| `$1, $2` | Argumentos | `./script.sh arg1 arg2` |
| `$(cmd)` | Subcomando | `FECHA=$(date)` |
| `$((A+B))` | Matemáticas | `SUMA=$((2+2))` |
| `if [ ... ]; then` | Condicional | Cuidado con los espacios `[ ]`. |
| `-eq, -gt` | Comparar Números | `-eq` (equal), `-gt` (greater). |
| `=, !=` | Comparar Texto | `if [ "$A" = "si" ]` |
| `-f, -d` | Test Archivos | `-f` (file), `-d` (directory). |
| `&&` | AND lógico | Ejecuta si el anterior fue OK. |
| `||` | OR lógico | Ejecuta si el anterior falló. |
| `for X in Y; do` | Bucle | `for f in *.txt; do ... done` |

Ahora tienes el poder. Ya no tienes que hacer tareas aburridas. Escribe un script, dale permisos, y deja que la máquina trabaje por ti mientras te tomas un café.
