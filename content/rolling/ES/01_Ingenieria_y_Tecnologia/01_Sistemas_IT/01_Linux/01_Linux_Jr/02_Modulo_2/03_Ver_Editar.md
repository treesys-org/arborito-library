@title: Leer y editar texto en terminal: cat, less, nano, vim
@icon: 📝
@description: Ver contenido de archivos y editarlos desde la CLI; introducción práctica a herramientas de texto habituales.
@order: 3

# Ver y editar archivos de texto desde la terminal

Bienvenido a una de las habilidades más críticas que adquirirás como usuario de Linux.

En Windows o macOS, estás acostumbrado a que los archivos se abran con programas pesados y específicos: Word para `.docx`, Bloc de Notas para `.txt`, Excel para `.xlsx`. Es una relación "Archivo-Programa".

En Linux, la filosofía es diferente. **Todo es texto**.
La configuración de tu red es texto. La lista de usuarios es texto. Los logs del sistema son texto. Los scripts que automatizan servidores son texto. Incluso la información sobre tu hardware se presenta como texto virtual.

Si dominas las herramientas para leer y editar texto en la terminal, dominas el sistema operativo. No necesitas interfaces gráficas. No necesitas ratón. Solo necesitas tus ojos y tu teclado.

Esta guía es extensa porque el tema lo requiere. Vamos a cubrir desde cómo leer una nota rápida hasta cómo sobrevivir en `vim`, el editor que ha atrapado a millones de novatos (literalmente, no sabían cómo salir).

@section: 1. Visualización Rápida: La Familia 'cat'

A veces solo quieres ver qué hay dentro de un archivo sin abrir un editor. Quieres volcar el contenido en tu pantalla. Para eso tenemos una familia de comandos muy útiles.

### 1.1 `cat` (Concatenate)
Es el comando más famoso, pero a menudo malentendido. Su nombre viene de "Concatenar" (unir), aunque el 99% de la gente lo usa para "mostrar".

**Uso Básico:**
```bash
$ cat archivo.txt
```
Esto imprime todo el contenido del archivo en la salida estándar (tu pantalla) de golpe.

**El Problema de `cat`:**
Si el archivo tiene 50.000 líneas, `cat` las imprimirá todas a la velocidad de la luz. Tu terminal se llenará de texto desplazándose tan rápido que no podrás leer nada, y al final solo verás las últimas 20 líneas.
*   **Regla:** Usa `cat` solo para archivos pequeños (archivos de configuración cortos, notas breves).

**Usos Avanzados de `cat`:**
*   **Numerar líneas:** A veces quieres saber en qué número de línea está algo.
    ```bash
    $ cat -n codigo.py
    ```
*   **Crear archivos rápidos:** Puedes usar `cat` para escribir archivos cortos sin abrir un editor, usando redirección.
    ```bash
    $ cat > lista_compra.txt
    Leche
    Pan
    Huevos
    (Pulsa Ctrl+D para guardar y salir)
    ```
    *Explicación:* El símbolo `>` redirige lo que escribes en el teclado hacia el archivo. `Ctrl+D` envía la señal de "Fin de Archivo" (EOF - End Of File).

### 1.2 `tac` (Cat al revés)
Sí, los programadores de Unix tienen un sentido del humor peculiar.
`tac` hace exactamente lo mismo que `cat`, pero imprime las líneas **en orden inverso**. La última línea aparece la primera.

**¿Para qué sirve esto?**
Imagina un archivo de log cronológico donde lo más nuevo se escribe al final. Si quieres ver lo último primero, `tac` es tu amigo.

```bash
$ tac eventos.log
```

### 1.3 `nl` (Number Lines)
Es una alternativa más específica a `cat -n`. Su único trabajo es numerar líneas.
```bash
$ nl guion.txt
```

@section: 2. Controlando el Flujo: Paginadores (less y more)

Cuando los archivos son demasiado grandes para `cat`, entran en juego los **Paginadores**. Son programas que te permiten leer el texto de forma interactiva, desplazándote hacia arriba y hacia abajo, sin cargar todo el archivo en la memoria a la vez.

### 2.1 `more` (El Abuelo)
Fue el primer paginador. Hoy en día es obsoleto, pero sigue presente en casi todos los sistemas por compatibilidad.
*   Solo permite ir hacia adelante (pulsando Espacio).
*   No permite volver atrás fácilmente.
*   Cuando llega al final, se cierra solo.
*   *Consejo:* No lo uses a menos que no tengas `less`.

### 2.2 `less` (El Estándar Moderno)
Hay un dicho en Linux: **"less is more"** (menos es más).
`less` es una versión mejorada de `more`. Es increíblemente potente.

**Uso Básico:**
```bash
$ less archivo_gigante.log
```

Al ejecutarlo, la terminal se convierte en un lector de libros electrónicos.

**Guía de Navegación en `less` (¡Memoriza esto!):**

| Acción | Tecla |
| :--- | :--- |
| **Bajar una línea** | `Enter` o `Flecha Abajo` o `j` |
| **Subir una línea** | `Flecha Arriba` o `k` |
| **Bajar una página** | `Espacio` o `AvPág` |
| **Subir una página** | `b` o `RePág` |
| **Ir al final** | `G` (Mayúscula) |
| **Ir al principio** | `g` (Minúscula) |
| **Buscar texto** | `/texto_a_buscar` (luego pulsa `n` para siguiente, `N` para anterior) |
| **Ayuda** | `h` |
| **SALIR** | `q` (Quit) |

**Por qué `less` es genial:**
`less` no carga todo el archivo en la RAM. Si tienes un archivo de registro de 100 GB (sí, pasa en servidores), `cat` colgaría tu ordenador intentando leerlo. `less` lo abre instantáneamente porque solo lee la parte que estás viendo en la pantalla.

@section: 3. Mirando las Puntas: head y tail

A menudo no quieres leer todo el libro. Solo quieres leer el prólogo o el epílogo.

### 3.1 `head` (La Cabeza)
Muestra las primeras líneas de un archivo. Por defecto, las primeras 10.

```bash
$ head /etc/passwd
```

**Personalizando la cantidad:**
Usa la opción `-n` para especificar cuántas líneas quieres.
```bash
$ head -n 5 /etc/passwd  # Muestra las 5 primeras
```

### 3.2 `tail` (La Cola)
Muestra las últimas líneas de un archivo. Por defecto, las últimas 10.
Esto es vital en administración de sistemas, porque los errores recientes en los logs siempre están al final.

```bash
$ tail /var/log/syslog
```

### 3.3 `tail -f` (El Modo Seguimiento)
Esta es quizás la función más usada por los administradores en todo el mundo.
La opción `-f` significa **Follow** (Seguir).

Cuando ejecutas `tail -f archivo.log`:
1.  Muestra las últimas 10 líneas.
2.  **NO se cierra.** Se queda esperando.
3.  Si algún programa escribe una nueva línea en ese archivo, `tail` la imprime en tu pantalla instantáneamente.

Es como ver una retransmisión en directo de lo que le pasa a tu servidor.

**Ejemplo de uso real:**
Estás intentando arrancar un servidor web y falla.
1.  Abres una terminal.
2.  Escribes: `tail -f /var/log/nginx/error.log`
3.  En otra terminal, reinicias el servidor.
4.  Ves el error aparecer en tiempo real en la primera terminal.
5.  Pulsas `Ctrl + C` para salir del modo seguimiento.

@quiz: Estás monitorizando un servidor y quieres ver los errores nuevos a medida que ocurren en el archivo 'error.log'. ¿Qué comando usas?
@option: cat error.log
@option: less error.log
@correct: tail -f error.log
@option: head -f error.log

@section: 4. Editores de Texto: Filosofía CLI

Ya sabemos leer. Ahora toca escribir.

Editar archivos en la terminal es intimidante al principio porque **no tienes ratón**. No puedes hacer clic en la mitad de una frase para corregir una errata. Tienes que usar las flechas o atajos de teclado para mover el cursor.

Existen dos "religiones" principales en el mundo de los editores de terminal:
1.  **nano:** Fácil, intuitivo, para humanos normales.
2.  **vi / vim:** Potente, complejo, para magos del teclado.

Vamos a aprender los dos. Necesitas `nano` para sobrevivir hoy, y necesitas entender `vi` para sobrevivir mañana.

@section: 5. `nano`: El Editor Amigable

Si eres nuevo en Linux, **usa nano**. No intentes ser un héroe todavía.
`nano` se diseñó para ser un reemplazo del antiguo editor `pico`. Su filosofía es: "La pantalla debe decirte qué hacer".

### Iniciando nano
```bash
$ nano mi_archivo.txt
```
Si el archivo no existe, `nano` lo creará en memoria (no se guarda en disco hasta que tú lo digas).

### La Interfaz de nano
En la parte superior ves la versión y el nombre del archivo.
En el centro, el área de texto.
**En la parte inferior**, ves una "Barra de Ayuda". Esto es lo que hace a nano genial.

Verás cosas como:
`^G Get Help`  `^O Write Out`  `^X Exit`

El símbolo `^` (caret) representa la tecla **Control (Ctrl)**.
Así que `^X` significa que debes pulsar `Ctrl + X`.

### Comandos Esenciales de nano

1.  **Escribir:** Simplemente teclea. Usa las flechas para moverte.
2.  **Guardar:** Se llama "Write Out" (Escribir fuera).
    *   Pulsa `Ctrl + O`.
    *   Nano te preguntará: `File Name to Write: mi_archivo.txt`.
    *   Pulsa `Enter` para confirmar.
3.  **Salir:**
    *   Pulsa `Ctrl + X`.
    *   Si no has guardado cambios, te preguntará: `Save modified buffer? (Y/N)`.
        *   Pulsa `Y` para sí, `N` para no.
        *   Si pulsaste `Y`, te pedirá confirmar el nombre del archivo. Pulsa `Enter`.
4.  **Buscar Texto:**
    *   Pulsa `Ctrl + W` (Where is / Dónde está).
    *   Escribe la palabra y pulsa `Enter`.
5.  **Cortar y Pegar (Estilo Nano):**
    *   Nano no usa el portapapeles normal del sistema por defecto.
    *   `Ctrl + K` (Cut Text): Corta (borra) la línea entera donde está el cursor.
    *   `Ctrl + U` (Uncut Text): Pega la línea que acabas de cortar.

### Configurando nano
Nano puede tener resaltado de sintaxis (colores) para programación. Normalmente se activa automáticamente si el archivo tiene extensión (ej. `.py` o `.html`).
Si quieres configurarlo, su archivo es `~/.nanorc`.

**¿Por qué usar nano?**
*   Está instalado en casi todas partes.
*   No requiere memorizar nada; las instrucciones están en la pantalla.
*   Es rápido y ligero.

@section: 6. `vi` y `vim`: La Bestia

Ahora entramos en territorio legendario. `vi` (Visual Editor) nació en 1976. `vim` (Vi IMproved) nació en 1991.
Es el editor estándar de Unix. Está en **todos** los sistemas Linux, desde tu router WiFi hasta el servidor más potente de Google. A veces, en sistemas de rescate mínimos, `nano` no está, pero `vi` siempre está.

**Tienes que saber lo básico de vi para no quedarte atrapado.**

### La Filosofía Modal
Aquí es donde la gente se confunde. Los editores normales (Notepad, nano, Word) tienen un solo modo: si tecleas una 'a', aparece una 'a' en la pantalla.

**Vim tiene MODOS.**
Dependiendo del modo en el que estés, la tecla 'a' puede significar "escribir la letra a" o puede significar "añadir texto después del cursor".

Los 3 modos principales:
1.  **Modo Normal (Normal Mode):** Es el modo por defecto al abrir. **Aquí no puedes escribir texto.** Las teclas son comandos para moverte, borrar, copiar o pegar. Es el "modo de control".
2.  **Modo Insertar (Insert Mode):** Aquí es donde escribes texto normal. Se comporta como un editor clásico.
3.  **Modo Comando (Command Line Mode):** Sirve para dar órdenes al editor (guardar, salir, buscar). Se entra pulsando `:`.

### Guía de Supervivencia en Vim (Paso a Paso)

Vamos a simular una sesión. No solo leas esto, **hazlo** en tu terminal.

**Paso 1: Entrar**
```bash
$ vi prueba_vim.txt
```
Ahora estás en **Modo Normal**. Si intentas escribir "Hola", pasarán cosas raras y el ordenador pitará. No entres en pánico.

**Paso 2: Escribir (Entrar en Modo Insertar)**
Para empezar a escribir, necesitas cambiar de modo.
*   Pulsa la tecla `i` (de Insert).
*   Fíjate en la esquina inferior izquierda. Debería decir `-- INSERT --`.
*   Ahora puedes escribir: "Hola mundo, esto es vim."

**Paso 3: Volver al Control (Salir de Modo Insertar)**
Ya has terminado de escribir. Quieres guardar. Pero no puedes guardar mientras escribes. Tienes que volver al Modo Normal.
*   Pulsa la tecla `Esc` (Escape).
*   El texto `-- INSERT --` desaparece. Estás de nuevo en Modo Normal.

**Paso 4: Guardar y Salir (Modo Comando)**
Desde el Modo Normal:
*   Pulsa `:` (dos puntos). Verás que aparece un `:` abajo del todo. El cursor te espera allí.
*   Escribe `w` (Write / Guardar).
*   Pulsa `Enter`. Vim dirá algo como "prueba_vim.txt written".
*   Pulsa `:` otra vez.
*   Escribe `q` (Quit / Salir).
*   Pulsa `Enter`.

¡Felicidades! Has sobrevivido a tu primera edición.

**El Atajo Pro:**
Puedes combinar comandos. Para guardar y salir a la vez:
*   `Esc`
*   `:wq`
*   `Enter`

**Paso 5: Salir SIN guardar (Pánico)**
Imagina que has borrado medio archivo por error y quieres salir sin guardar los cambios.
*   `Esc` (Para asegurar que estás en normal).
*   `:q`
*   Vim te gritará: *"No write since last change (add ! to override)"*. Te protege.
*   Para forzar la salida: `:q!` (Quit Bang!).

### Movimiento en Modo Normal
¿Por qué la gente ama Vim? Porque en Modo Normal puedes moverte y editar a la velocidad del pensamiento sin tocar el ratón.

*   **h, j, k, l:** Mueven el cursor (Izquierda, Abajo, Arriba, Derecha). ¿Por qué no las flechas? Porque en los teclados antiguos no había flechas, y porque así no tienes que mover las manos de la posición de mecanografía. (Las flechas funcionan, pero los pros usan hjkl).
*   **w:** Salta a la siguiente palabra (Word).
*   **b:** Salta a la palabra anterior (Back).
*   **0:** Va al principio de la línea.
*   **$:** Va al final de la línea.
*   **gg:** Va al principio del archivo.
*   **G:** Va al final del archivo.

### Edición Rápida en Modo Normal
*   **x:** Borra el carácter bajo el cursor.
*   **dd:** Borra (corta) la línea entera actual.
*   **u:** Deshacer (Undo). ¡El salvavidas!
*   **Ctrl + r:** Rehacer.
*   **yy:** Copia (Yank) la línea actual.
*   **p:** Pega (Paste) lo que has cortado o copiado después del cursor.

**La Gramática de Vim:**
Vim es un lenguaje. Los comandos se pueden combinar con números y movimientos.
*   `d` (delete) + `w` (word) = `dw` (Borrar una palabra).
*   `d` (delete) + `$` (fin de línea) = `d$` (Borrar hasta el final de la línea).
*   `2` (dos veces) + `dd` (borrar línea) = `2dd` (Borrar dos líneas).
*   `100` + `j` = Bajar 100 líneas.

Una vez que interiorizas esta "gramática", editar texto se convierte en algo fluido y rapidísimo.

### `vimtutor`
Vim viene con un tutorial interactivo fantástico. Si quieres aprender de verdad, escribe en tu terminal:
```bash
$ vimtutor
```
Te llevará unos 30 minutos completarlo y te enseñará más que cualquier libro.

@quiz: Estás atrapado en `vi` y no sabes en qué modo estás. Quieres salir sin guardar nada. ¿Qué secuencia exacta de teclas debes pulsar?
@option: Ctrl+C, luego :exit
@correct: Esc, luego :q!, luego Enter
@option: Ctrl+X, luego N
@option: Escribir "exit" y pulsar Enter

@section: 7. Comparativa: ¿Cuál elegir?

| Característica | nano | vi / vim |
| :--- | :--- | :--- |
| **Curva de Aprendizaje** | Plana (5 minutos) | Vertical (Semanas) |
| **Interfaz** | Menús visibles, atajos Ctrl | Modos invisibles, atajos de una tecla |
| **Uso de Ratón** | No (generalmente) | No |
| **Velocidad de Edición** | Normal | Extremadamente alta (para expertos) |
| **Disponibilidad** | Muy común | Universal (está en todo) |
| **Uso Ideal** | Ediciones rápidas, novatos | Programación, SysAdmin profesional |

**Mi consejo:**
1.  Usa `nano` para empezar. No te frustres. Edita tus archivos de configuración con nano.
2.  Aprende los comandos mínimos de `vi` (`i`, `Esc`, `:wq`, `:q!`) por si algún día te encuentras en un servidor que no tiene nano.
3.  Si planeas dedicarte a esto profesionalmente, dedica una semana a aprender `vim`. Tus manos te lo agradecerán a largo plazo.

@section: 8. Otros Comandos de Texto Útiles

Aparte de ver y editar, hay herramientas para manipular el texto en "tuberías" (pipes).

*   `wc` (Word Count): Cuenta líneas, palabras y caracteres.
    *   `wc -l archivo.txt`: Cuenta solo las líneas.
*   `sort`: Ordena las líneas de un archivo alfabéticamente.
    *   `sort nombres.txt`
*   `uniq`: Elimina líneas duplicadas **consecutivas**. (Suele usarse después de `sort`).
*   `diff`: Compara dos archivos y te dice qué diferencias hay entre ellos línea por línea.
    *   `diff version1.conf version2.conf`

@section: 9. Resumen / Cheat Sheet

**Visualización:**
*   `cat`: Archivos pequeños, todo de golpe.
*   `less`: Archivos grandes, paginación, búsqueda (`/`), salir con `q`.
*   `head`: Principio del archivo.
*   `tail`: Final del archivo.
*   `tail -f`: Ver logs en tiempo real.

**Edición con Nano:**
*   `Ctrl + O`: Guardar.
*   `Ctrl + X`: Salir.
*   `Ctrl + W`: Buscar.

**Edición con Vim:**
*   `i`: Modo Insertar (Escribir).
*   `Esc`: Volver a Modo Normal.
*   `:w`: Guardar.
*   `:q`: Salir.
*   `:q!`: Salir sin guardar (Forzar).
*   `:wq`: Guardar y Salir.
*   `dd`: Borrar línea.
*   `u`: Deshacer.

¡Ahora tienes el poder de controlar cualquier archivo de texto en cualquier sistema Unix del mundo!
