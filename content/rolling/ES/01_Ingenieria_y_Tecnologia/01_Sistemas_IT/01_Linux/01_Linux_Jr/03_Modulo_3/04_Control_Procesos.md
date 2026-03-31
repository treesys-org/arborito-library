@title: Procesos: ps, top, señales y jobs en segundo plano
@icon: 🚦
@description: Listar y monitorizar procesos, PIDs, señales (kill), prioridades básicas y tareas en foreground/background.
@order: 4

# Gestión de procesos en Linux: listar, monitorizar y enviar señales

Bienvenido al centro de mando de tu ordenador.

Hasta ahora, hemos tratado los archivos como cosas estáticas que viven en el disco duro. Pero un ordenador apagado con el disco duro lleno no sirve de nada. La magia ocurre cuando esos archivos "cobran vida".

Cuando ejecutas un programa (como Firefox, Python o un simple `ls`), el Sistema Operativo toma ese código muerto del disco, lo carga en la memoria RAM, le asigna recursos y le da un turno para usar la CPU. En ese momento, el programa deja de ser un "programa" y se convierte en un **Proceso**.

Tu sistema Linux es una ciudad bulliciosa. En este mismo instante, mientras lees esto, hay cientos de procesos corriendo en tu máquina.
*   Algunos están trabajando frenéticamente (tu navegador renderizando esta página).
*   Otros están durmiendo, esperando a que pulses una tecla.
*   Otros son "demonios" invisibles que vigilan la red o la hora.

Como Administrador de Sistemas, tú eres el **Director de Orquesta** (o el Alcalde de esta ciudad).
Tienes el poder absoluto para:
1.  Ver quién está consumiendo demasiados recursos (CPU/RAM).
2.  Decidir quién vive y quién muere (Kill).
3.  Cambiar las prioridades (quién es más importante).
4.  Mandar tareas al fondo para que no molesten.

En esta guía masiva, vamos a diseccionar la anatomía de un proceso, entenderemos qué demonios es el "Load Average", aprenderemos a matar procesos de forma educada (y no tan educada) y dominaremos el arte de la multitarea en la terminal.

Prepárate. Vamos a mirar dentro del cerebro de la máquina.

@section: 1. Anatomía de un Proceso

Antes de sacar el bisturí, necesitamos entender al paciente.
Para el Kernel de Linux, un proceso no es "Firefox". Es una estructura de datos con propiedades muy específicas.

### 1.1 El DNI: PID (Process ID)
En el mundo humano, nos llamamos por nombres. En el mundo del Kernel, los nombres no importan. Lo único que importa es el **PID**.

Cada proceso que nace recibe un número único: el **Process ID**.
*   Es un número entero positivo.
*   Se asignan secuencialmente.
*   El proceso **número 1** es siempre **systemd** (o init). Es el "Padre de Todos". Si matas al 1, el sistema se apaga (Kernel Panic).
*   Cuando llegas al límite (usualmente 32768 o más), el contador vuelve a empezar, saltándose los que están ocupados.

**Regla de Oro:** Para controlar un proceso, necesitas saber su PID.

### 1.2 El Árbol Genealógico: PPID (Parent Process ID)
Los procesos no aparecen por generación espontánea (excepto el PID 1).
Todo proceso es creado por otro proceso.
*   Si abres una terminal (`bash`) y ejecutas `firefox`, entonces `bash` es el PADRE de `firefox`.
*   El **PPID** de Firefox será el PID de Bash.

Esta jerarquía es vital. Si matas al padre, a veces los hijos mueren también, o a veces se quedan huérfanos (lo veremos luego).

### 1.3 El Dueño: UID y GID
Todo proceso pertenece a un usuario (User ID) y a un grupo (Group ID).
Esto determina qué puede hacer el proceso.
*   Si ejecutas `cat /etc/shadow` como tu usuario normal, el proceso `cat` nace con tu UID. Al intentar leer el archivo, el Kernel ve que tu UID no tiene permiso y el proceso falla.
*   Si ejecutas `sudo cat /etc/shadow`, el proceso nace con UID 0 (root) y tiene éxito.

@section: 2. `ps`: La Foto Instantánea

El comando `ps` (Process Status) es la herramienta más antigua y estándar para ver qué está pasando.
Es como una cámara de fotos: te muestra el estado de los procesos en el instante exacto en que pulsaste Enter.

### 2.1 El `ps` inútil
Si escribes simplemente `ps` en la terminal:
```bash
$ ps
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 5678 pts/0    00:00:00 ps
```
Esto no sirve de casi nada. Solo te muestra los procesos que estás ejecutando **tú** en **esa terminal**.

### 2.2 El Estándar de Oro: `ps aux`
Para ver TODO lo que pasa en el sistema, usamos las opciones BSD (sin guiones, aunque con guiones también suele funcionar). Memoriza esto: **`ps aux`**.

*   `a`: Todos los procesos (all), no solo los tuyos.
*   `u`: Formato orientado a usuario (user), con detalles como %CPU y Memoria.
*   `x`: Procesos que no tienen terminal (daemons/servicios en segundo plano).

Ejecútalo:
```bash
$ ps aux | head -n 5
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 168436 13244 ?        Ss   oct20   0:05 /sbin/init
root         2  0.0  0.0      0     0 ?        S    oct20   0:00 [kthreadd]
...
```

### 2.3 Descifrando las Columnas de `ps aux`
Esta tabla es densa. Vamos a explicarla columna por columna, porque aquí está la verdad del sistema.

1.  **USER:** El dueño del proceso. (Ej: `root`, `www-data`, `juan`).
2.  **PID:** El identificador único. (Ej: `1234`).
3.  **%CPU:** Qué porcentaje del procesador está usando *ahora mismo*.
4.  **%MEM:** Qué porcentaje de tu RAM física está ocupando.
5.  **VSZ (Virtual Memory Size):** La cantidad de memoria virtual que el proceso ha *reservado*.
    *   *Ojo:* Esto suele ser un número gigante y mentiroso. Un programa puede pedir "resérvame 1GB" pero solo usar 1MB. VSZ muestra el 1GB.
6.  **RSS (Resident Set Size):** La cantidad de memoria RAM física real que está usando. **Este es el número que te importa** si te estás quedando sin RAM.
7.  **TTY:** En qué terminal está corriendo. Si ves `?`, es un demonio (servicio de fondo).
8.  **STAT:** El estado del proceso (Ver sección de Estados más abajo).
9.  **START:** Cuándo arrancó.
10. **TIME:** Cuánto tiempo total de CPU ha consumido desde que nació.
11. **COMMAND:** El comando exacto que lanzó el proceso (incluyendo argumentos).

### 2.4 La Alternativa: `ps -ef`
En el mundo corporativo (especialmente en Unix antiguos o Red Hat), verás a gente usar `ps -ef`.
*   `-e`: Every (Todo).
*   `-f`: Full format.

Muestra información similar a `aux` pero en diferente orden y muestra el **PPID** (Padre) claramente, lo cual es útil para ver jerarquías.

@section: 3. Filtrando el Ruido (`grep`)

`ps aux` te escupe 300 líneas. Tú solo quieres saber si el servidor web "nginx" está corriendo.
Usamos tuberías.

```bash
$ ps aux | grep nginx
```

Resultado típico:
```text
root      1050  0.0  0.1  ... nginx: master process /usr/sbin/nginx
www-data  1051  0.0  0.2  ... nginx: worker process
juan      2040  0.0  0.0  ... grep --color=auto nginx
```

**El fantasma de grep:**
Fíjate en la última línea. ¡Es el propio comando `grep` buscándose a sí mismo!
Cuando ejecutas `ps`, el comando `grep` también está corriendo, así que sale en la foto.
Para evitar esto, un truco ninja es:
```bash
$ ps aux | grep [n]ginx
```
(Esto busca la cadena "nginx", pero el proceso se llama "grep [n]ginx", por lo que no coincide consigo mismo. Magia de expresiones regulares).

@quiz: Estás analizando por qué tu servidor se ha quedado sin memoria RAM. ¿Qué columna de `ps aux` es la más fiable para ver el consumo real de memoria física de un proceso?
@option: VSZ
@correct: RSS
@option: TTY
@option: TIME

@section: 4. `top`: El Monitor en Vivo

`ps` es una foto. `top` es un vídeo.
`top` actualiza la lista de procesos cada 3 segundos (por defecto). Es el cuadro de mandos que todo administrador tiene abierto en una pantalla secundaria.

Ejecútalo:
```bash
$ top
```

### 4.1 El Encabezado (El Resumen del Sistema)
Las primeras 5 líneas de `top` contienen información vital sobre la salud global de tu máquina.

**Línea 1: Uptime y Load Average**
`top - 10:00:01 up 15 days, 2 users, load average: 0.50, 1.10, 1.20`
*   **up 15 days:** El servidor lleva 15 días sin reiniciarse.
*   **load average:** Esto es CRÍTICO. Son tres números que representan la carga media en el último **1 minuto**, **5 minutos** y **15 minutos**.

**¿Qué es el Load Average? (La Analogía del Puente)**
Imagina un puente (tu CPU).
*   Si el Load es **0.0**, el puente está vacío.
*   Si el Load es **0.5**, el puente está al 50% de ocupación. El tráfico fluye.
*   Si el Load es **1.0**, el puente está lleno, pero fluye.
*   Si el Load es **2.0**, el puente está lleno Y hay una cola de coches del mismo tamaño esperando para entrar.

**Regla:** Un Load de 1.0 significa que una CPU está al 100%.
*   Si tienes 4 núcleos (CPUs), un Load de 4.0 es el 100%. Un Load de 2.0 es el 50%.
*   Si los números bajan (ej: 1.20 -> 1.10 -> 0.50), el problema se está resolviendo.
*   Si los números suben (ej: 0.50 -> 1.10 -> 5.00), ¡corre!

**Línea 2: Tareas (Tasks)**
Cuántos procesos hay en total, cuántos corriendo, durmiendo o zombies.

**Línea 3: %Cpu(s)**
*   `us` (User): Tiempo gastado en programas de usuario (tu navegador).
*   `sy` (System): Tiempo gastado en tareas del Kernel.
*   `id` (Idle): Tiempo que la CPU está rascándose la barriga (libre).
*   `wa` (Wait I/O): **¡IMPORTANTE!** Tiempo que la CPU está parada esperando al disco duro. Si este número es alto, tu disco es lento o está roto.

### 4.2 Interactuando con `top`
`top` no es solo para mirar. Puedes controlarlo con teclas:
*   **M** (Mayúscula): Ordenar por uso de **Memoria**. (Vital para encontrar fugas de RAM).
*   **P** (Mayúscula): Ordenar por uso de **CPU** (Defecto).
*   **k**: Matar un proceso. Te pedirá el PID.
*   **1**: Si tienes varios núcleos, los desglosa uno por uno.
*   **q**: Salir.

### 4.3 `htop`: La Evolución
`top` es feo y viejo. Si puedes instalar software (`sudo apt install htop`), hazlo.
**`htop`** hace lo mismo pero con colores, barras gráficas, uso del ratón y una interfaz mucho más humana. Es la herramienta preferida hoy en día.

@section: 5. El Ciclo de Vida: Estados de un Proceso

En la columna STAT de `ps` o `top`, verás letras extrañas. Indican qué está haciendo el proceso.

1.  **R (Running):** Está usando la CPU o listo para usarla ya.
2.  **S (Sleeping):** Está durmiendo. Esperando a que pase algo (que llegue un paquete de red, que escribas una tecla). La mayoría de procesos están así.
3.  **D (Uninterruptible Sleep):** El estado "Peligroso". Está esperando al Hardware (Disco duro) y **no puede ser interrumpido**. Ni siquiera `kill -9` puede matar a un proceso en estado D. Tienes que esperar a que el disco responda (o reiniciar si el disco está roto).
4.  **Z (Zombie):** Muertos vivientes.
    *   Un proceso Zombie ha terminado su trabajo y ha muerto, pero su "Padre" no ha leído su testamento (código de salida).
    *   No consumen CPU ni RAM. Solo ocupan una entrada en la tabla de PIDs.
    *   No se pueden matar (ya están muertos). Para limpiarlos, tienes que matar al Padre.

@quiz: Ves un proceso en estado 'D' en `top` que está bloqueando el sistema. Intentas matarlo con `kill -9` pero no desaparece. ¿Por qué?
@option: Necesitas ser root para matarlo.
@option: El proceso es un virus.
@correct: El proceso está en 'Uninterruptible Sleep' esperando hardware (I/O); no puede procesar señales hasta que el hardware responda.
@option: Es un proceso Zombie.

@section: 6. `kill`: El Arte del Asesinato Digital

A veces, un programa se cuelga. O consume demasiada memoria. Hay que terminarlo.
El comando se llama `kill`, pero su nombre es engañoso. `kill` no mata directamente; `kill` **envía señales**.

**Sintaxis:**
`kill [SEÑAL] PID`

Hay docenas de señales, pero solo necesitas memorizar estas 4:

### 1. SIGTERM (Señal 15) - "Por favor, termina"
Es la señal por defecto si no especificas nada.
*   Es educada. Le dice al programa: *"Oye, por favor, cierra tus archivos, guarda lo que puedas y sal"*.
*   El programa puede decidir ignorarla o tardar un rato.
*   **Uso:** `kill 1234`

### 2. SIGKILL (Señal 9) - "El Francotirador"
Es la señal de fuerza bruta.
*   No se le envía al programa. El Kernel la intercepta y **destruye** el proceso instantáneamente.
*   El programa no puede limpiar nada, ni guardar, ni despedirse. Puede dejar archivos corruptos.
*   **Uso:** `kill -9 1234`
*   *Consejo:* Úsala solo si SIGTERM no funciona.

### 3. SIGINT (Señal 2) - "Interrupción"
Es lo mismo que pulsar `Ctrl + C` en la terminal. Es una petición brusca de parada.

### 4. SIGHUP (Señal 1) - "Cuelga y Recarga"
Históricamente significaba "Hang Up" (colgar el teléfono/módem).
Hoy se usa para decirle a los demonios (como servidores web): *"No te mueras, pero relee tus archivos de configuración"*.
*   **Uso:** `kill -1 1234` (Ideal para aplicar cambios de config sin apagar el servicio).

### Matando por Nombre: `pkill` y `killall`
Buscar el PID con `ps`, copiarlo y hacer `kill` es lento.

*   **`pkill`**: Mata buscando por nombre parcial.
    `pkill fire` (Matará a firefox).
*   **`killall`**: Mata buscando por nombre EXACTO.
    `killall firefox`
    **¡PELIGRO CON KILLALL!** En algunos sistemas Unix comerciales (Solaris, AIX), `killall` significa **"Kill All Processes"** (Matar TODO el sistema). Si trabajas en servidores enterprise viejos, ten cuidado. En Linux es seguro.

@section: 7. Jobs: Foreground y Background

La terminal es monohilo: ejecutas un comando y esperas. Pero Linux es multitarea. Puedes lanzar cosas al fondo.

### El Operador `&` (Ampersand)
Si añades `&` al final de un comando, se ejecuta en **Background** (segundo plano). Te devuelve el control de la terminal inmediatamente.

```bash
$ sleep 60 &
[1] 4567
```
El sistema te responde: `[1]` (Número de trabajo/Job) y `4567` (PID).

### Controlando Trabajos
Imagina que lanzas un script largo sin `&` y te bloquea la terminal. ¿Tienes que cancelarlo y empezar de nuevo? **No.**

1.  **Pausar:** Pulsa `Ctrl + Z`.
    *   El proceso se detiene (se congela, estado T) y pasa al fondo.
    *   `[1]+  Stopped                 ./script_largo.sh`
2.  **Ver trabajos:** Ejecuta `jobs`.
    *   Verás la lista de tareas en esa terminal.
3.  **Reanudar en Fondo (`bg`):**
    *   Escribe `bg %1`.
    *   El proceso 1 se despierta y sigue trabajando, pero en el fondo (`&`). Ya puedes usar la terminal.
4.  **Traer al Frente (`fg`):**
    *   Escribe `fg %1`.
    *   El proceso vuelve a primer plano y toma el control de la terminal (para ver su salida o interactuar).

**El problema del cierre de sesión (nohup):**
Si lanzas un proceso en background y cierras la terminal (o se corta el SSH), **el proceso muere**. (Recibe la señal SIGHUP).
Para evitar esto y dejar cosas corriendo eternamente:
```bash
$ nohup ./script_largo.sh &
```
`nohup` (No Hang Up) inmuniza al proceso contra el cierre de la terminal. La salida se guardará en `nohup.out`.

@section: 8. Prioridades: Nice y Renice

No todos los procesos son iguales. Quizás quieres que tu renderizado de vídeo use toda la CPU, o al revés, que se haga despacito sin ralentizar tu navegación web.

Linux usa un valor llamado **Niceness** (Amabilidad).
*   Va de **-20** (El más egoísta, máxima prioridad) a **+19** (El más amable, mínima prioridad).
*   Por defecto, todos nacen con **0**.
*   **Regla:** Cuanto más alto el número, más "amable" es el proceso (deja pasar a otros). Cuanto más bajo (negativo), más prioridad tiene.

**Lanzar con prioridad (`nice`):**
```bash
# Lanzar un backup con baja prioridad (muy amable) para no molestar
$ nice -n 19 tar -czf backup.tar.gz /home
```

**Cambiar prioridad en vuelo (`renice`):**
```bash
# Darle máxima prioridad a un juego (PID 555)
# Solo root puede bajar el valor (dar más prioridad).
$ sudo renice -n -10 -p 555
```

@section: 9. Laboratorio Práctico: El Proceso Inmortal

Vamos a jugar con la vida y la muerte.

1.  **Crear una víctima:**
    Abre una terminal y ejecuta un proceso eterno que no hace nada:
    ```bash
    $ sleep 1000
    ```
    La terminal está bloqueada.

2.  **Pausar:**
    Pulsa `Ctrl + Z`. Verás "Stopped".

3.  **Mandar al fondo:**
    Escribe `bg`. Ahora el sleep corre en segundo plano.

4.  **Verificar:**
    Escribe `jobs`. Verás que está "Running".
    Escribe `ps aux | grep sleep`. Verás su PID.

5.  **El Asesinato Suave:**
    Escribe `kill [PID]` (usa el PID que viste).
    Escribe `jobs` otra vez. Debería poner "Terminated".

6.  **El Asesinato Zombie (Teórico):**
    Si matas un proceso y se queda como `<defunct>` en `ps`, es un zombie. No intentes matarlo más veces. Busca su PPID (Padre) en `ps -ef` y mata al padre. El sistema limpiará al hijo.

@section: Resumen / Cheat Sheet

| Comando | Acción | Truco Clave |
| :--- | :--- | :--- |
| `ps aux` | Foto de todos los procesos | Busca la columna PID y RSS (RAM). |
| `top` | Monitor en tiempo real | Mira el "Load Average" arriba. |
| `htop` | Monitor mejorado | Instálalo si puedes. |
| `kill PID` | Terminar (SIGTERM - 15) | Pide por favor que cierre. |
| `kill -9 PID` | Matar (SIGKILL - 9) | Fuerza bruta. Úsalo si el 15 falla. |
| `Ctrl + C` | Cancelar proceso actual | Es la señal SIGINT. |
| `Ctrl + Z` | Pausar proceso actual | Luego usa `bg` o `fg`. |
| `jobs` | Ver trabajos de esta terminal | |
| `bg %N` | Reanudar en fondo | |
| `fg %N` | Traer al frente | |
| `nice` | Ejecutar con prioridad | `nice -n 19` para tareas pesadas de fondo. |

¡Felicidades! Ahora sabes gestionar los recursos de tu máquina mejor que el propio sistema operativo. Eres el dueño del tiempo de tu CPU.