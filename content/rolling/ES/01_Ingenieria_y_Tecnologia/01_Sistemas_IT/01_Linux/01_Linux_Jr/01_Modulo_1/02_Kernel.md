@title: Kernel Linux y distribuciones: qué es cada pieza
@icon: 📦
@description: Diferencia entre kernel y distro, paquetes base y por qué “usar Linux” suele significar una distribución completa.
@order: 2

# Kernel Linux frente a la distribución: piezas del sistema operativo

Bienvenido a la clase de biología digital. Hoy vamos a diseccionar a Tux, el pingüino de Linux.

Si vienes de Windows o macOS, probablemente tengas una idea muy monolítica de lo que es un sistema operativo. Piensas en "Windows" como una sola cosa gigante que te vende Microsoft. No puedes separar la barra de tareas del núcleo del sistema; todo viene en un paquete indivisible. Si no te gusta el explorador de archivos de Windows, te aguantas.

**Linux es diferente.** Linux no es un bloque de cemento; es una caja de **LEGO**.

Cuando la gente dice "Uso Linux", en realidad está usando una frase hecha que es técnicamente incorrecta. Lo que usan es una **Distribución** que contiene el **Kernel Linux** junto con miles de otras piezas de software.

En este capítulo, vamos a romper este mito y explicar, pieza por pieza, cómo funciona este rompecabezas, usando analogías que cualquiera puede entender.

@section: 1. El Kernel (El Corazón y el Cerebro)

Empecemos por lo básico. **Linux es un Kernel.** Nada más.

Cuando Linus Torvalds escribió la primera versión en 1991, no escribió un navegador web, ni un editor de texto, ni un entorno de ventanas. Escribió un **Kernel** (Núcleo).

### ¿Qué es exactamente un Kernel?
Imagina un restaurante muy concurrido.
*   **El Hardware (CPU, RAM, Disco):** Es la cocina, llena de ingredientes, hornos y cuchillos afilados.
*   **Las Aplicaciones (Chrome, Spotify, Word):** Son los clientes en las mesas, pidiendo cosas ("Quiero guardar este archivo", "Quiero dibujar esto en la pantalla").
*   **El Kernel:** Es el **Jefe de Cocina** y el **Gerente**.

Los clientes (aplicaciones) NUNCA entran a la cocina (hardware). Sería un caos. Se quemarían, robarían ingredientes o se pelearían por el horno.
En su lugar, los clientes le pasan notas al Kernel. El Kernel decide:
1.  **Quién cocina ahora:** "Spotify, tú usas la CPU (el horno) durante 5 milisegundos. Luego te quitas y entra Chrome".
2.  **Quién usa qué ingredientes:** "Firefox, tú tienes 4GB de RAM. No toques la RAM de Excel o te echo del restaurante".
3.  **Seguridad:** "Juego.exe, estás intentando escribir en una zona prohibida del disco duro. ¡Estás despedido!" (El Kernel cierra el programa).

### Ubicación Física
En tu disco duro, el Kernel es solo un archivo. Normalmente vive en la carpeta `/boot` y tiene un nombre extraño como `vmlinuz-6.8.0-generic`.
Cuando enciendes el PC, este archivo se carga en la memoria RAM y se queda allí, vigilando todo, hasta que apagas la máquina. Es el primer programa en despertar y el último en dormir.

### Responsabilidades Críticas
Si el Kernel falla, todo falla. Sus tareas son vitales:
*   **Gestión de Memoria:** Lleva la contabilidad de cada byte de RAM. Si se acaba la RAM, decide qué programa matar para salvar al resto (esto se llama *OOM Killer* o "Asesino de Fuera de Memoria").
*   **Drivers (Controladores):** El Kernel es el único que sabe hablar "idioma tarjeta gráfica" o "idioma tarjeta WiFi". Traduce las peticiones genéricas de los programas a señales eléctricas para el hardware.
*   **Sistema de Archivos:** Entiende cómo leer y escribir datos en el disco sin corromperlos.

@quiz: ¿Por qué las aplicaciones no acceden directamente al hardware en un sistema moderno?
@option: Porque no saben cómo hacerlo.
@correct: Por seguridad y estabilidad; el Kernel actúa como intermediario para evitar conflictos y caos.
@option: Porque el hardware es propiedad de Microsoft.

@section: 2. Espacio de Kernel vs. Espacio de Usuario

Para protegerse a sí mismo de programas mal escritos o maliciosos, Linux divide la memoria en dos zonas sagradas. Esta es la defensa principal del sistema.

### Ring 0: Espacio de Kernel (Kernel Space)
*   **Acceso:** Total y absoluto al hardware.
*   **Residentes:** Solo el código del Kernel y sus drivers más críticos.
*   **Peligro:** Si hay un error aquí (un bug), el sistema entero colapsa. En Windows, esto es la "Pantalla Azul de la Muerte". En Linux, se llama **Kernel Panic**. El sistema se congela y parpadean luces para evitar daños mayores.

### Ring 3: Espacio de Usuario (User Space)
*   **Acceso:** Restringido. No pueden tocar el hardware directamente. Viven en una simulación segura ("Sandbox") creada por el Kernel.
*   **Residentes:** TODO lo demás. Tu navegador, tu entorno de escritorio, tu servidor web, tu shell, tus juegos.
*   **Seguridad:** Si un programa aquí intenta hacer algo ilegal (como leer la memoria de otro programa), el Kernel lo detecta y lo mata instantáneamente (Segmentation Fault). Pero el resto del sistema sigue funcionando felizmente.

**La magia de las System Calls (Llamadas al Sistema):**
Cuando tu navegador quiere guardar una descarga en el disco, no puede hacerlo él mismo. Tiene que usar una "Llamada al Sistema" (como `write()`). Es como tocar el timbre de la puerta del Kernel y decir: *"Por favor, Señor Kernel, escriba estos datos en el disco por mí"*. El Kernel verifica si tienes permisos, y si es así, lo hace.

@section: 3. La Distribución (El Coche Completo)

Muy bien, tienes el Kernel. Es un motor V12 increíble, potente y eficiente. Lo tienes tirado en el suelo de tu garaje.
¿Puedes ir al supermercado con él? **No.**
Te faltan las ruedas, el volante, los asientos, el chasis y la carrocería.

Aquí es donde entra la **Distribución** (o Distro).

Una Distro es un proyecto (mantenido por una empresa o una comunidad) que hace el trabajo sucio por ti:
1.  Toman el Kernel de Linux (el motor).
2.  Le añaden herramientas de sistema GNU (las ruedas y la transmisión).
3.  Le añaden un entorno gráfico (la carrocería y el diseño interior).
4.  Le añaden programas preinstalados (la radio y el aire acondicionado).
5.  Empaquetan todo en un instalador fácil de usar.

### Componentes de una Distro Típica
Para que te hagas una idea de lo complejo que es esto, una distro moderna como Ubuntu incluye:
*   **Bootloader (GRUB):** El programa que arranca el Kernel.
*   **Init System (Systemd):** El primer proceso que arranca todos los demás servicios (WiFi, sonido, red).
*   **Shell (Bash/Zsh):** La interfaz de texto.
*   **Servidor Gráfico (X11/Wayland):** La capa que sabe dibujar píxeles en la pantalla.
*   **Entorno de Escritorio:** Ventanas, menús, iconos.
*   **Gestor de Paquetes:** La tienda de aplicaciones.

Cuando dices "Instalar Linux", en realidad estás diciendo "Instalar una Distribución de GNU/Linux".

@section: 4. El Entorno de Escritorio (La Cara Visible)

En Windows, el escritorio es Windows. No puedes cambiarlo. En Linux, el escritorio es solo otro programa más. Si no te gusta, lo borras e instalas otro. Puedes tener cinco instalados y elegir cuál usar al iniciar sesión.

Esto confunde mucho a los novatos. *"¿Por qué mi Linux se ve diferente al de mi amigo?"*. Probablemente usáis la misma distro (el mismo motor) pero diferente Entorno de Escritorio (diferente carrocería).

Aquí están los "Cuatro Grandes":

#### 1. GNOME (El Modernista)
*   **Filosofía:** Simplicidad, minimalismo, flujo de trabajo único.
*   **Se parece a:** Una mezcla entre macOS y un iPad.
*   **Características:** No tiene barra de tareas clásica ni botón de inicio. Usas una tecla "Super" para ver todas las ventanas y buscar apps.
*   **¿Quién lo usa?**: Ubuntu, Fedora (por defecto), Debian.

#### 2. KDE Plasma (El Personalizable)
*   **Filosofía:** Potencia total, configuración infinita. "Si se puede programar, se puede configurar".
*   **Se parece a:** Windows 10/11 (por defecto), pero puedes hacer que parezca un Mac o algo futurista.
*   **Características:** Muy ligero hoy en día (sorprendentemente). Tiene miles de opciones. Puede abrumar al principio.
*   **¿Quién lo usa?**: Kubuntu, Fedora KDE, Steam Deck (sí, la consola de Valve usa KDE).

#### 3. XFCE (El Clásico)
*   **Filosofía:** Estabilidad, ligereza, recursos mínimos.
*   **Se parece a:** Windows 95/XP o Mac antiguo.
*   **Características:** Roca sólida. No tiene animaciones lujosas ni efectos 3D. Funciona en ordenadores de hace 15 años como si fueran nuevos.
*   **¿Quién lo usa?**: Xubuntu, Linux Mint XFCE.

#### 4. Cinnamon (El Familiar)
*   **Filosofía:** Hacer que los usuarios de Windows se sientan en casa.
*   **Se parece a:** Windows 7.
*   **Características:** Menú de inicio clásico, barra de tareas abajo, bandeja de sistema. Muy intuitivo.
*   **¿Quién lo usa?**: Linux Mint (es su escritorio insignia).

@quiz: Tienes un ordenador muy viejo con poca memoria RAM y quieres instalar Linux. ¿Qué entorno de escritorio sería la recomendación más lógica?
@option: GNOME
@option: KDE Plasma con efectos 3D
@correct: XFCE
@option: Windows 11

@section: 5. Las Familias Reales: Genealogía de Distros

Hay miles de distros, pero casi todas son "hijas" o "nietas" de tres grandes familias originales. Entender esto te ayuda a saber qué tutoriales seguir.

### La Familia Debian (.deb)
Es la familia más grande y popular en el mundo del escritorio y servidores web. Usan el sistema de paquetes `apt` y archivos `.deb`.

*   **Debian:** La Matriarca. Fundada en 1993. Es un proyecto 100% comunitario, sin empresa detrás. Su obsesión es la estabilidad y el software libre. Es la base de todo.
*   **Ubuntu:** La Hija Pródiga. Creada por la empresa Canonical basándose en Debian. Su misión: "Linux para seres humanos". Hizo Linux fácil de instalar.
*   **Linux Mint:** La Nieta Rebelde. Basada en Ubuntu pero quitando cosas polémicas de Canonical y añadiendo un escritorio muy fácil (Cinnamon). Es la recomendación #1 para principiantes hoy.
*   **Kali Linux:** La Nieta Ninja. Basada en Debian, llena de herramientas de hacking. NO es para uso diario.

### La Familia Red Hat (.rpm)
Dominan el mundo empresarial corporativo. Usan el sistema `dnf` (antes `yum`) y archivos `.rpm`.

*   **RHEL (Red Hat Enterprise Linux):** El producto comercial de IBM/Red Hat. Cuesta dinero (mucho) y tiene soporte técnico 24/7. Es lo que usan los bancos y gobiernos.
*   **Fedora:** El campo de pruebas. Es comunitario y gratuito. Red Hat prueba aquí las tecnologías nuevas. Si funcionan bien, años después acaban en RHEL. Es excelente para desarrolladores que quieren lo último.
*   **AlmaLinux / Rocky Linux:** Los Clones. Son copias exactas (bit a bit) de RHEL, pero gratuitas y sin soporte técnico oficial. Se usan en servidores que quieren la estabilidad de RHEL sin pagar.

### La Familia Arch (Rolling)
Para usuarios avanzados que quieren control total y software nuevo cada día. Usan `pacman`.

*   **Arch Linux:** La base. La instalas escribiendo comandos en una pantalla negra. Tú decides cada pieza que instalas. "KISS: Keep It Simple, Stupid".
*   **Manjaro:** Arch para seres humanos. Tiene instalador gráfico y lo hace todo más fácil, pero mantiene la base de Arch.

@section: 6. Modelos de Lanzamiento: ¿Estabilidad o Novedad?

Esta es una decisión crítica al elegir tu "coche". ¿Quieres un coche que nunca se averíe pero que tenga tecnología de hace 5 años, o un coche con la última tecnología experimental que podría fallar el domingo por la mañana?

### Modelo 1: Fixed Release (Lanzamiento Fijo / LTS)
*   **Cómo funciona:** Como Windows o macOS. Sale una versión "24.04" en abril de 2024. Esa versión se congela. Durante 5 años, solo recibe parches de seguridad. Las versiones de los programas NO cambian. Si tienes LibreOffice 7.0, tendrás LibreOffice 7.0 para siempre en esa versión.
*   **Ventaja:** **Estabilidad extrema**. Sabes que mañana tu PC funcionará exactamente igual que hoy. Ideal para servidores y trabajo crítico.
*   **Desventaja:** **Software viejo**. Si sale una nueva característica en Photoshop (versión Linux), no la tendrás hasta que actualices todo el sistema operativo a la siguiente versión mayor (dentro de 2 años).
*   **Ejemplos:** Debian Stable, Ubuntu LTS, RHEL, Linux Mint.

### Modelo 2: Rolling Release (Lanzamiento Continuo)
*   **Cómo funciona:** No hay versiones. No existe "Arch 2.0". Instalas el sistema una vez y actualizas poco a poco para siempre. Si hoy sale la versión 15 de un programa, mañana (o en horas) la tienes disponible. El sistema es un río que fluye.
*   **Ventaja:** **Siempre a la última**. Tienes el último Kernel, los últimos drivers de NVIDIA y las últimas apps.
*   **Desventaja:** **Riesgo de rotura**. A veces, una actualización de una librería cambia algo y rompe un programa que dependía de la versión antigua. Tienes que estar más atento.
*   **Ejemplos:** Arch Linux, Manjaro, openSUSE Tumbleweed.

### Modelo 3: Semi-Rolling (Punto Medio)
*   Distros como **Fedora** lanzan versiones cada 6 meses, pero mantienen el software (Kernel y drivers) muy actualizado durante esos 6 meses. Es un gran equilibrio.

@section: 7. FAQ para Noobs (Preguntas Frecuentes)

**P: ¿Es Android un Linux?**
R: Sí... y no. Android usa el **Kernel de Linux** (el motor) para gestionar la memoria y el hardware del móvil. Pero no usa el sistema GNU ni las aplicaciones estándar de Linux. Han construido una casa diferente sobre los mismos cimientos. No puedes ejecutar apps de Android en Ubuntu directamente, ni apps de Ubuntu en Android, sin emuladores.

**P: ¿Puedo ejecutar programas de Windows (.exe) en Linux?**
R: Directamente, no. Linux no entiende .exe. Sin embargo, existe una capa de compatibilidad llamada **WINE** (y herramientas como Proton de Steam) que "traducen" las peticiones de Windows a Linux en tiempo real. Gracias a esto, hoy en día puedes jugar a miles de juegos de Windows en Linux perfectamente. Pero no es garantía 100%.

**P: ¿Tengo que usar la terminal obligatoriamente?**
R: En 2025, **NO**. Distros modernas como Linux Mint o Ubuntu se pueden usar 100% con ratón, instalando apps desde una "Tienda de Software" gráfica y configurando el WiFi con clics. Sin embargo, aprender la terminal (que es lo que enseña este curso) te da un superpoder: control absoluto y velocidad que ninguna interfaz gráfica puede igualar.

**P: ¿Qué distro debería instalar hoy mismo si soy nuevo?**
R: Mi recomendación incuestionable: **Linux Mint (Edición Cinnamon)**. Es estable, se parece a Windows (no te sentirás perdido), tiene todo preinstalado y una comunidad gigantesca. Si prefieres algo más moderno visualmente, prueba **Ubuntu**.

@quiz: ¿Cuál es el principal riesgo de usar una distribución "Rolling Release"?
@option: El software es demasiado viejo.
@option: Cuesta dinero.
@correct: Una actualización reciente podría contener errores (bugs) que rompan algo del sistema.
@option: No tiene interfaz gráfica.

@section: Glosario Rápido

*   **Bootloader:** El portero que te deja pasar (GRUB).
*   **Kernel:** El jefe de cocina (Linux).
*   **Daemon:** Un proceso que corre en segundo plano (servicio).
*   **Shell:** El intérprete de tus comandos (Bash).
*   **CLI:** Command Line Interface (Pantalla negra con letras).
*   **GUI:** Graphical User Interface (Ventanas y ratón).
*   **Repo (Repositorio):** Almacén seguro de software de tu distro.
*   **Sudo:** "SuperUser DO". La palabra mágica para tener poderes de administrador temporalmente.

¡Felicidades! Ahora sabes más sobre cómo funciona tu ordenador que el 95% de la población. Estás listo para dejar de ser un pasajero y empezar a ser el mecánico.
