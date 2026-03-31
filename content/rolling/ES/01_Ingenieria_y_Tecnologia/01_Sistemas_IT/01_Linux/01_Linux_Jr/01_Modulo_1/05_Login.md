@title: Interfaces en Linux: TTY, shell, consola y escritorio gráfico
@icon: 🖥️
@description: Capas con las que hablas con el sistema: terminales, sesión de texto, shell y entorno gráfico.
@order: 5

# Terminal, shell y escritorio gráfico: capas de la interfaz en Linux

Has encendido el ordenador. El Kernel está gestionando la memoria. Systemd ha arrancado los servicios. El disco duro gira (o los electrones fluyen en tu SSD). Todo está listo.

Pero ahora la máquina se detiene y te mira fijamente (metafóricamente). Está esperando una orden. Necesita un **Input** (entrada) tuyo para generar un **Output** (salida).

Aquí es donde entras tú. Pero, ¿cómo le hablas? ¿Le hablas en clics de ratón? ¿Le hablas en comandos de texto? ¿Le gritas a la pantalla?

En este capítulo, vamos a diseccionar la **Interfaz de Usuario** de Linux. Si vienes de Windows o macOS, probablemente pienses que la "interfaz" es simplemente las ventanas y los iconos. En Linux, la realidad es mucho más profunda, flexible y, a veces, confusa si no entiendes las capas.

Vamos a pelar la cebolla de la interfaz de Linux, capa por capa, hasta entender por qué los hackers de las películas escriben en pantallas negras con letras verdes y por qué tu escritorio de Linux puede verse como un Mac, un Windows o una nave espacial según cómo lo configures.

@section: 1. La Filosofía de los Dos Mundos

Linux tiene una personalidad dividida, como el Dr. Jekyll y Mr. Hyde.

1.  **El Mundo Gráfico (GUI - Graphical User Interface):**
    *   Es lo que ves al arrancar: ventanas, iconos, ratón, colores.
    *   Es intuitivo: "Veo un archivo, hago clic en él".
    *   Es limitado: Solo puedes hacer lo que los programadores pusieron en los menús. Si no hay un botón para "renombrar 5000 fotos según su fecha", no puedes hacerlo fácilmente.
    *   Es pesado: Consume mucha memoria RAM y CPU solo para dibujar los bordes bonitos de las ventanas.

2.  **El Mundo de Texto (CLI - Command Line Interface):**
    *   Es una pantalla negra con letras.
    *   Es abstracto: No ves los archivos hasta que pides verlos (`ls`).
    *   Es ilimitado: Puedes combinar comandos para hacer cosas que los programadores originales ni siquiera imaginaron.
    *   Es ligero: Funciona en ordenadores de hace 30 años o en servidores superpotentes sin gastar recursos en gráficos.

**La Regla de Oro del SysAdmin:**
> "Las interfaces gráficas (GUI) hacen que las tareas sencillas sean fáciles. La línea de comandos (CLI) hace que las tareas difíciles sean posibles."

@section: 2. Diseccionando la Línea de Comandos (CLI)

Es muy común que los novatos (y no tan novatos) confundan tres términos: **Consola**, **Terminal** y **Shell**. La gente los usa indistintamente, pero técnicamente son cosas muy diferentes. Vamos a ser precisos.

### A. La Shell (El Cerebro)
La Shell (cáscara o concha) es un **programa de software**. No es una ventana. No es un teclado. Es un intérprete.
Su trabajo es:
1.  Leer el texto que escribes.
2.  Entenderlo (Parsear).
3.  Buscar el programa que has pedido.
4.  Ejecutarlo.
5.  Devolverte el resultado en texto.

La Shell es un lenguaje de programación en tiempo real. Puedes escribir bucles, variables y condiciones directamente en ella.

**Tipos de Shells:**
Como todo en Linux, puedes elegir tu Shell.
*   **Bash (Bourne Again Shell):** Es el estándar de facto. Viene por defecto en casi todas las distribuciones (Ubuntu, Debian, Fedora, Red Hat). Es robusta, fiable y compatible. Si aprendes Bash, sabes Linux.
*   **Zsh (Z Shell):** Es la alternativa moderna y "cool". Es compatible con Bash pero añade características de calidad de vida: autocompletado inteligente, corrección ortográfica de comandos, temas visuales avanzados. Es el defecto en macOS y Kali Linux.
*   **Fish (Friendly Interactive Shell):** Diseñada para ser amigable desde el segundo 0. Tiene colores y sugerencias automáticas increíbles, pero **no** es 100% compatible con el estándar de Bash, por lo que a veces los scripts fallan.

### B. El Emulador de Terminal (La Boca y los Oídos)
La Shell no sabe dibujar una ventana gris en tu escritorio. La Shell no sabe cambiar el tipo de letra a "Monospace 12pt". La Shell ni siquiera sabe que tienes un ratón.

Para eso necesitas un **Emulador de Terminal**.
Es un programa gráfico (una aplicación normal como Chrome o Word) que abre una ventana en tu entorno de escritorio.
*   Cuando pulsas una tecla, la Terminal la captura y se la envía a la Shell.
*   Cuando la Shell responde con texto, la Terminal decide cómo pintarlo (color, fuente, tamaño) en la pantalla.

**Ejemplos de Emuladores:**
*   **GNOME Terminal:** El clásico de Ubuntu.
*   **Konsole:** El potente emulador de KDE.
*   **Alacritty / Kitty:** Emuladores modernos acelerados por GPU (tarjeta gráfica) para que el texto vuele.

**Atajos de Teclado Vitales en la Terminal:**
¿Alguna vez has intentado copiar texto en la terminal con `Ctrl+C` y no ha funcionado?
*   En Windows/Mac: `Ctrl+C` es Copiar.
*   En la Terminal de Linux: `Ctrl+C` es una señal de interrupción (**CANCELAR**). Le dice al programa que se está ejecutando: "¡Muérete ahora mismo!".
*   **Para Copiar:** Debes usar `Ctrl + Shift + C`.
*   **Para Pegar:** Debes usar `Ctrl + Shift + V`.

### C. El Prompt (El Aviso)
Cuando abres una terminal, ves algo críptico esperando a que escribas. Eso es el **Prompt**.
Típicamente se ve así:
`usuario@maquina:~$`

Vamos a decodificarlo:
1.  `usuario`: Quién eres ahora mismo.
2.  `@`: Separador "en".
3.  `maquina`: El nombre del ordenador (Hostname). Útil si administras 50 servidores remotamente para saber dónde estás.
4.  `:`: Separador.
5.  `~`: Dónde estás (Tu directorio actual). La virgulilla `~` es una abreviatura de "Mi Carpeta Personal" (`/home/usuario`). Si entras en `/etc`, esto cambiará.
6.  `$`: El símbolo de rango.
    *   `$`: Significa que eres un usuario normal (mortal).
    *   `#`: Significa que eres **root** (dios). Ten mucho cuidado si ves un `#`.

**Personalización (.bashrc):**
Puedes cambiar el aspecto de tu Shell editando un archivo oculto en tu carpeta personal llamado `.bashrc` (si usas Bash) o `.zshrc` (si usas Zsh).
Es un script que se ejecuta cada vez que abres una terminal. Ahí puedes crear "Alias" (atajos).
*Ejemplo:* `alias actualizar='sudo apt update && sudo apt upgrade'`
Ahora, cada vez que escribas `actualizar`, el sistema ejecutará todo el comando largo.

@section: 3. Las TTYs: El Búnker Subterráneo

Ahora vamos a profundizar. Debajo de tus ventanas bonitas, debajo de tu ratón y tus animaciones, Linux mantiene vivas las viejas tradiciones de los años 70.

Linux es un sistema **multiusuario** y **multisesión**.
Imagina que tu ordenador es un edificio de oficinas.
*   El **Entorno Gráfico** es el ático de lujo con vistas, aire acondicionado y sillones cómodos.
*   Pero el edificio tiene sótanos. Oficinas de hormigón, sin ventanas, solo con una mesa y una máquina de escribir. Esos sótanos son las **TTYs (Teletypewriters)**.

### ¿Qué es una TTY?
Históricamente, un Teletipo era una máquina física (parecida a una máquina de escribir conectada por cable) que permitía enviar texto al ordenador central (Mainframe).
Hoy en día, Linux "virtualiza" estas máquinas.

Tu sistema Linux normalmente tiene **6 o 7 consolas virtuales** ejecutándose siempre en paralelo.
*   Normalmente, tu entorno gráfico vive en la **TTY1**, **TTY2** o **TTY7** (depende de la distribución, Ubuntu suele usar TTY1 o TTY2).
*   Las demás están libres, ejecutando una pantalla negra de login, esperando.

### El Superpoder de la Teletransportación
Puedes saltar entre estas consolas físicas usando una combinación de teclas mágica que funciona a un nivel muy bajo del Kernel.

**¡PRÁCTICA DE RIESGO CERO! (Hazlo ahora):**
1.  Estás leyendo esto en tu navegador gráfico.
2.  Pulsa simultáneamente: `Ctrl` + `Alt` + `F3`.
3.  **¡Pánico!** La pantalla se ha vuelto negra (o gris). Ha desaparecido el ratón. Solo ves texto blanco pidiendo un login.
    *   *Tranquilo, no has roto nada. Solo has bajado al sótano número 3.*
4.  Escribe tu nombre de usuario y pulsa Enter.
5.  Escribe tu contraseña (no verás asteriscos ni nada, es por seguridad) y pulsa Enter.
6.  ¡Estás dentro! Tienes una Shell completa. Puedes usar `ls`, `top`, `nano`. Es un ordenador plenamente funcional, pero solo texto.
7.  Ahora, vamos a volver al ático. Pulsa `Ctrl` + `Alt` + `F1` (o F2, si F1 no funciona).
8.  ¡Magia! Estás de vuelta en tu navegador, exactamente donde lo dejaste.

### ¿Para qué sirve esto en 2025?
No es solo curiosidad histórica. Es tu **salida de emergencia**.

**Escenario de Pesadilla:**
Estás jugando a un videojuego o renderizando un vídeo 4K. De repente, el sistema gráfico explota. La pantalla se congela. El ratón no se mueve. La música se queda rayada ("trrr-trrr-trrr").
En Windows, tu única opción es el "Botonazo" (apagar manteniendo el botón de encendido), lo cual puede corromper tus datos o tu disco duro.

**La Solución Linux:**
1.  El entorno gráfico se ha colgado, pero el **Kernel** (el núcleo) sigue vivo. El Kernel gestiona el teclado.
2.  Pulsas `Ctrl` + `Alt` + `F3`. El Kernel recibe la orden y cambia la salida de vídeo a la TTY3 (que es texto simple y casi nunca falla).
3.  Te logueas en la TTY3.
4.  Ejecutas `top` o `htop` para ver qué proceso está consumiendo el 100% de la CPU (el juego colgado).
5.  Lo matas con `kill -9 [PID]`.
6.  Vuelves a tu entorno gráfico (`Ctrl` + `Alt` + `F1`). ¡El juego se ha cerrado pero tu escritorio ha revivido! Has salvado la sesión sin reiniciar.

@quiz: Tu entorno gráfico está totalmente congelado y el ratón no responde. ¿Qué combinación de teclas es la mejor primera opción para intentar recuperar el control?
@option: Ctrl + Alt + Supr
@correct: Ctrl + Alt + F3 (Acceder a una TTY)
@option: Alt + F4
@option: Botón de Reset físico

@section: 4. El Stack Gráfico: ¿Cómo se dibuja un píxel?

Si decides quedarte en el entorno gráfico, debes entender cómo funciona. En Windows y Mac, el sistema gráfico es parte del Kernel. En Linux, **no**.
En Linux, la interfaz gráfica es **opcional**. Un servidor Linux funciona perfectamente sin ella.
Para tener ventanas, instalamos una pila (stack) de software sobre el Kernel. Es como un sándwich de muchas capas.

### Capa 1: El Hardware (GPU) y el Kernel (DRM/KMS)
*   Tienes una tarjeta gráfica (NVIDIA, AMD, Intel).
*   El Kernel tiene drivers para hablar con ella.
*   El Kernel usa un sistema llamado **DRM** (Direct Rendering Manager) y **KMS** (Kernel Mode Setting) para decirle a la gráfica: *"Configura el monitor a 1920x1080 píxeles y prepárate para recibir dibujos"*.

### Capa 2: El Servidor Gráfico (Display Server)
Esta es la pieza de software que coordina el dibujo. Es el lienzo.
Aquí hay una guerra civil tecnológica en curso: **X11 vs Wayland**.

#### X11 (X Window System / X.Org)
*   **El Veterano:** Nació en 1984 (antes que Linux). Ha sido el estándar durante 30 años.
*   **Filosofía:** "Soy un servidor de red". X11 fue diseñado para que el programa se ejecutara en un mainframe gigante y la ventana se dibujara en tu terminal tonta a través de la red.
*   **El Problema:** Es viejo. Es un código monstruoso ("Spaghetti code"). Es inseguro por diseño (cualquier ventana puede espiar lo que escribes en otra ventana, lo cual hace que los keyloggers sean triviales). No gestiona bien las pantallas modernas de alta resolución (HiDPI) o múltiples monitores con diferentes refrescos.

#### Wayland
*   **El Joven Heredero:** Diseñado desde cero para reemplazar a X11.
*   **Filosofía:** "Soy simple y seguro". Elimina la complejidad de red antigua.
*   **Seguridad:** Aísla las ventanas. Una ventana no puede ver ni capturar lo que hace otra a menos que tú le des permiso explícito (por eso compartir pantalla en Zoom/Discord a veces daba problemas en Wayland al principio).
*   **Estado:** En 2025, la mayoría de distros modernas (Ubuntu, Fedora) usan Wayland por defecto. Es el futuro. Es más fluido, sin "tearing" (rasgado de imagen) y más seguro.

### Capa 3: El Compositor y el Gestor de Ventanas (WM)
El Servidor Gráfico provee la superficie. Pero, ¿quién dibuja los bordes? ¿Quién decide dónde va cada ventana? ¿Quién hace las sombras y las transparencias?
*   **Window Manager (WM):** Dibuja los marcos, la barra de título, los botones de minimizar/cerrar. Gestiona que si mueves el ratón y haces clic, la ventana se mueva.
*   **Compositor:** Añade efectos visuales. Sombras, transparencias, desenfoques, animaciones al abrir/cerrar.

En la era de **X11**, eran programas separados.
En la era de **Wayland**, el Servidor Gráfico, el WM y el Compositor suelen ser un solo programa fusionado para mayor eficiencia.

### Capa 4: El Entorno de Escritorio (Desktop Environment - DE)
Esto es lo que tú ves y llamas "Sistema Operativo".
El DE es un paquete completo que incluye:
1.  El Gestor de Ventanas.
2.  Paneles (barras de tareas).
3.  Menús de aplicaciones.
4.  Explorador de archivos (Nautilus, Dolphin).
5.  Configuración del sistema (WiFi, Bluetooth, Pantalla).
6.  Un set de aplicaciones básicas (Calculadora, Visor de fotos).

Esta es la gran libertad de Linux. **Puedes elegir tu Entorno de Escritorio**.
Si instalas Windows, tienes el escritorio de Windows. Punto.
Si instalas Linux, puedes elegir entre docenas de paradigmas de interacción humano-computadora.

@section: 5. Galería de Entornos: Elige tu Sabor

Cuando descargas una distribución (como Ubuntu o Fedora), suele venir con un DE por defecto ("Flavour"), pero puedes instalar otros. Aquí están los principales:

### 1. GNOME (El Visionario)
*   **Lema:** "Fuera distracciones".
*   **Estilo:** Muy diferente a Windows. Se parece más a macOS o a una tablet iPadOS.
*   **Funcionamiento:** No hay barra de tareas tradicional ni botón de inicio. Tienes una barra superior fina. Pulsas la tecla "Super" (Windows) y ves todas tus ventanas abiertas y un lanzador de aplicaciones.
*   **Filosofía:** Minimalismo extremo. Ocultan opciones para no abrumar al usuario. Flujo de trabajo basado en búsquedas y espacios de trabajo virtuales.
*   **Quién lo usa:** Ubuntu (por defecto), Fedora (por defecto), Debian.

### 2. KDE Plasma (El Ingeniero)
*   **Lema:** "Potencia y Control Total".
*   **Estilo:** Por defecto se parece a Windows 10/11. Panel inferior, menú de inicio a la izquierda, reloj a la derecha.
*   **Filosofía:** Personalización infinita. Puedes cambiar TODO. ¿Quieres que la barra esté arriba? Hecho. ¿Quieres que las ventanas sean gelatinosas? Hecho. ¿Quieres que parezca un Mac? Hecho.
*   **Potencia:** Es increíblemente ligero hoy en día (consume menos RAM que GNOME), pero tiene miles de opciones de configuración. Puede abrumar a quien solo quiere que funcione.
*   **Quién lo usa:** Kubuntu, KDE Neon, Steam Deck (Modo Escritorio).

### 3. XFCE (El Estoico)
*   **Lema:** "Estable y Ligero".
*   **Estilo:** Retro. Recuerda a Windows 95/XP o los UNIX de los 90.
*   **Filosofía:** La función sobre la forma. No tiene animaciones modernas ni desenfoques. Su objetivo es gastar la menor cantidad de recursos posible.
*   **Estabilidad:** Es una roca. Si configuras XFCE hoy, seguirá funcionando igual dentro de 10 años.
*   **Quién lo usa:** Xubuntu, Linux Mint XFCE edition. Ideal para revivir PCs viejos.

### 4. Tiling Window Managers (i3, Sway, Hyprland) - ¡Zona Hacker!
Esto no son Entornos de Escritorio completos, son solo Gestores de Ventanas.
*   **Filosofía:** El ratón es lento. El teclado es rápido.
*   **Funcionamiento:** No hay ventanas flotantes (que se tapan unas a otras). El sistema organiza las ventanas automáticamente en una cuadrícula ("Tiles" o baldosas) para aprovechar el 100% de la pantalla.
*   **Control:** Todo se hace con atajos de teclado. `Super+Enter` abre terminal. `Super+Shift+Q` cierra ventana. `Super+1` cambia al escritorio 1.
*   **Público:** Programadores y administradores de sistemas que quieren eficiencia máxima y sentirse en *Matrix*.

@quiz: Tienes un ordenador potente y te encanta personalizar cada detalle de tu interfaz, desde las animaciones hasta el color de las sombras. ¿Qué entorno eliges?
@option: GNOME
@correct: KDE Plasma
@option: XFCE
@option: Una TTY

@section: 6. SSH: La Telepatía Digital

Finalmente, hay una interfaz que no es ni local ni gráfica, pero es la más importante de todas para un profesional.

La mayoría de los servidores Linux **no tienen monitor**. Viven en racks en centros de datos fríos y ruidosos.
¿Cómo los controlas? Usando **SSH (Secure Shell)**.

SSH te permite abrir una ventana de terminal en TU ordenador, pero que los comandos se ejecuten en EL OTRO ordenador.
Es un túnel criptográfico seguro a través de Internet.

**El comando:**
`ssh usuario@direccion_ip`

**Autenticación por Llaves (Key-Based Auth):**
Aunque puedes usar contraseña, los pros usan llaves.
1.  Creas un par de llaves en tu PC (pública y privada).
2.  Copias la llave **Pública** al servidor (es como poner un candado tuyo en su puerta).
3.  Te guardas la llave **Privada** (es la única llave que abre ese candado).
4.  Cuando intentas entrar, el servidor ve tu candado, tu PC usa la llave privada para abrirlo, y entras sin escribir contraseña. Es mucho más seguro que una contraseña porque la llave privada es un archivo larguísimo imposible de adivinar.

@section: Resumen Final y Consejos

La interfaz de Linux es un ecosistema, no un producto único.

1.  **No tengas miedo a la Terminal:** Es tu amiga. Es la forma más rápida de hablar con el sistema.
2.  **Aprende a usar las TTYs:** Te salvarán cuando el entorno gráfico falle (y fallará algún día).
3.  **Experimenta con los Escritorios:** Lo bonito de Linux es que puedes probar GNOME una semana y KDE la siguiente sin formatear el ordenador (solo instalando el paquete). Encuentra el que se adapte a tu cerebro.
4.  **Entiende las capas:** Si algo falla visualmente, probablemente no sea "Linux" lo que falla, sino el servidor gráfico o el compositor. Saber esto te ayuda a buscar la solución en Google.

Has pasado de ver la pantalla como una "caja negra" a entender los engranajes que mueven los píxeles. ¡Bienvenido al control total!

@quiz: ¿Qué componente del stack gráfico es responsable de añadir efectos visuales como transparencias y sombras a las ventanas?
@option: El Kernel
@option: El Servidor Gráfico (X11)
@correct: El Compositor
@option: La Shell