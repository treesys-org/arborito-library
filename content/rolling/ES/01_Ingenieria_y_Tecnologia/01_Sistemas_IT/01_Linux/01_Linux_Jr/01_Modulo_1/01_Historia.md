@title: Historia: Unix, GNU y Linux (mainframes al código abierto)
@icon: 📜
@description: Cómo pasamos de mainframes y tarjetas perforadas a Unix, el proyecto GNU y Linux: contexto para entender tu sistema y el software libre hoy.
@order: 1

# Historia de Unix, GNU y Linux: mainframes, tarjetas perforadas y código abierto

Introducción narrativa: verás el hilo que va de los centros de cómputo de los 60, las guerras de Unix, el manifiesto GNU y el kernel de Linux, hasta el lugar que ocupan hoy en servidores, nube y móviles.

@section: Mapa LPIC-1 — Módulo 1 (referencia de cobertura)

Este módulo apoya objetivos de certificación tipo **LPIC-1 (examen 101)** en el eje de **arquitectura del sistema**, **conceptos de kernel y espacio de usuario**, **cadena de arranque** e **instalación**. No sustituye el boletín oficial de LPI; sirve como brújula.

*   **Tema 101 — Arquitectura del sistema:** de dónde sale el software libre; por qué existe el árbol de directorios; mentalidad multiusuario.
*   **Tema 101 — Arranque:** contexto histórico de init (SysV → systemd) enlazado con lecciones de kernel y arranque del mismo módulo.
*   **Tema 102 — Instalación:** particiones, gestores de arranque y modos de instalación (se profundiza en `03_Instalacion.md` y `04_Arranque.md`).
*   **Nota RHEL/Debian:** los nombres de paquetes (`dnf`/`apt`) y rutas (`/boot/grub2` vs `/boot/grub`) cambian; aquí priorizamos el **modelo mental** común.

Bienvenido a la verdadera historia de la informática moderna.

Es fácil caer en la trampa de pensar que Linux es simplemente "otro sistema operativo", una alternativa gratis a Windows que usan los servidores. Pero esa es una visión superficial y aburrida. Linux es el resultado improbable de una serie de rebeliones ideológicas, accidentes afortunados, egos desmedidos y guerras corporativas que abarcan más de medio siglo.

Para entender por qué tu terminal funciona como funciona, por qué escribes `ls` en lugar de `dir`, por qué el sistema es gratuito, o por qué Internet no colapsa, necesitamos excavar profundamente en el pasado. Esta no es una lista de fechas; es la historia de cómo un grupo de hippies, académicos y hackers derrotaron a las corporaciones más grandes del planeta usando solo texto.

@section: 1. La Era del Hierro: Sacerdotes y Mainframes (1960s)

Viajemos a mediados de la década de 1960. El concepto de "Ordenador Personal" (PC) era ciencia ficción pura, algo que solo se veía en *Star Trek*.

La informática estaba dominada por el **"Big Iron"** (Hierro Grande): los Mainframes. Eran máquinas colosales, propiedad de titanes como IBM, DEC (Digital Equipment Corporation) o General Electric. Vivían en santuarios refrigerados llamados "Centros de Cómputo", atendidas por una casta de técnicos con batas blancas que actuaban como sacerdotes entre los humanos y la máquina.

### La Tiranía del "Batch Processing" (Procesamiento por Lotes)
Si eras un científico o programador en 1965, tu vida era miserable. No tenías un teclado en tu escritorio. No tenías una pantalla. La interacción con la máquina era asíncrona, burocrática y frustrante:

1.  **Codificación Analógica:** Escribías tu programa en hojas de papel pautado con un lápiz.
2.  **La Perforación:** Llevabas esas hojas a una sala de mecanografía donde operadoras transferían tu código a **tarjetas perforadas** de cartón rígido. Cada tarjeta representaba una sola línea de código. Un programa complejo era una caja de zapatos llena de tarjetas.
3.  **La Ventanilla:** Entregabas tu caja a un operador. Él la ponía en una cola física junto a las de otros programadores.
4.  **El Proceso (Batch):** La máquina leía las tarjetas una tras otra, ejecutaba los programas en serie y escupía los resultados en una impresora de línea.
5.  **La Espera:** Podías esperar 4, 12 o 24 horas para obtener resultados.
6.  **El Horror:** Si habías cometido **un solo error de sintaxis** (una coma mal puesta), el programa fallaba al instante. Recibías una hoja de papel con el error y tenías que repetir todo el proceso desde el paso 1.

Esta lentitud sofocaba la creatividad. Los programadores pasaban el 90% de su tiempo esperando y el 10% trabajando. La informática era exclusiva, cara y lenta.

@section: 2. El Sueño y la Caída de Multics

En este contexto de frustración, surgió una alianza de titanes: el **MIT** (cerebros académicos), **General Electric** (hardware potente) y los **Laboratorios Bell de AT&T** (investigación y telecomunicaciones).

Su sueño era construir la utopía informática: **Multics** (Multiplexed Information and Computing Service).
La idea era revolucionaria: convertir la computación en un "servicio público" (utility), como la electricidad o el agua.
*   Un mainframe central gigantesco y omnipotente.
*   Cientos de terminales tontas (teclado y pantalla/impresora) en las oficinas de los usuarios, conectadas por líneas telefónicas.
*   **Time-Sharing (Tiempo Compartido):** La CPU cambiaría tan rápido entre usuarios que cada uno sentiría que tenía la máquina para él solo en tiempo real.

### El Fracaso
Multics fue víctima de lo que en ingeniería se llama "The Second System Effect" (El Efecto del Segundo Sistema): intentar hacerlo todo perfecto desde el principio.
Era monstruosamente complejo. Intentaron escribirlo en un lenguaje nuevo y no probado llamado **PL/1**. El hardware de la época apenas podía moverlo. El sistema tardaba minutos en arrancar, consumía demasiada memoria y se colgaba constantemente.

En **1969**, los ejecutivos de los Laboratorios Bell miraron las facturas, vieron que estaban tirando millones de dólares en un pozo sin fondo, y tomaron una decisión ejecutiva: **Matar el proyecto**. Bell Labs se retiró de Multics.

@section: 3. De las Cenizas: El Nacimiento de UNIX (1969)

La cancelación de Multics dejó a un grupo de brillantes ingenieros de los Bell Labs sin proyecto y, lo que es peor, sin su "juguete" de tiempo compartido. Entre ellos estaban **Ken Thompson**, **Dennis Ritchie** y **Brian Kernighan**.

Estaban acostumbrados a la interacción en tiempo real de Multics y se negaban a volver a la tiranía de las tarjetas perforadas. Además, Ken Thompson había escrito un videojuego llamado *Space Travel* (una simulación del sistema solar) y quería jugarlo. Correrlo en el mainframe de la compañía costaba 75 dólares por partida (en dinero de hoy, unos 600 dólares). Necesitaban su propia máquina.

Encontraron una vieja minicomputadora **PDP-7** de DEC abandonada en un pasillo. Tenía apenas 8KB de memoria (sí, kilobytes). Era una máquina ridícula comparada con los mainframes, pero estaba libre y nadie la vigilaba.

### El Verano del 69
Mientras su esposa e hijo se iban de vacaciones a California, Ken Thompson se encerró en el laboratorio durante un mes frenético (agosto de 1969). Decidió escribir un sistema operativo para esa PDP-7, pero aprendiendo de los errores de Multics.
En lugar de hacerlo complejo y grandioso, lo haría simple, pequeño y modular. **KISS: Keep It Simple, Stupid**.

En cuatro semanas escribió:
1.  Un núcleo (Kernel) básico para gestionar la CPU.
2.  Un sistema de archivos jerárquico (carpetas dentro de carpetas).
3.  Un intérprete de comandos (Shell).
4.  Un editor y un ensamblador.

Había nacido **UNICS** (Uniplexed Information and Computing Service), un juego de palabras burlón sobre "Multics" (Uniplexed = Uno, Multiplexed = Muchos). Más tarde, se deletreó **UNIX**.

### La Época (The Epoch)
Un detalle curioso: Para medir el tiempo, Unix cuenta los segundos que han pasado desde una fecha arbitraria. Esa fecha es el **1 de enero de 1970**. Ese es el "Big Bang" para tu ordenador. Si escribes `date +%s` en tu terminal hoy, verás los segundos transcurridos desde ese momento.

@section: 4. La Revolución del Lenguaje C (1972)

Al principio, Unix tenía un defecto fatal: estaba escrito en **Lenguaje Ensamblador** (Assembly).
El Ensamblador son instrucciones directas a la CPU (mueve este bit aquí, suma estos registros). Es extremadamente rápido, pero **no es portable**. El Ensamblador de una máquina PDP-7 es totalmente diferente al de una máquina IBM o una UNIVAC.
*   *El Problema:* Si querían llevar Unix a una computadora nueva y más potente (como la nueva PDP-11), tenían que **reescribir todo el sistema operativo desde cero**.

Dennis Ritchie, frustrado por esto, decidió crear un nuevo lenguaje de programación de "alto nivel" que permitiera controlar el hardware pero que fuera legible por humanos y portable entre máquinas.
Tomó un lenguaje anterior llamado B (creado por Thompson), lo mejoró, le añadió tipos de datos y estructuras, y lo llamó **C**.

En **1973**, Thompson y Ritchie cometieron una herejía: **Reescribieron el Kernel de Unix en C**.
Hasta entonces, se creía que los sistemas operativos debían escribirse en Ensamblador para ser rápidos. Ellos demostraron lo contrario.

**El Resultado: La Portabilidad**
De repente, Unix podía moverse. Podías tomar el código fuente C, llevarlo a una computadora con una arquitectura diferente, hacer pequeños ajustes en el compilador, y recompilar Unix. En cuestión de semanas, tenías el SO funcionando en hardware nuevo.
Esto permitió que Unix se extendiera como un virus benévolo por todas las universidades del mundo durante los años 70.

@section: 5. Las Guerras de Unix (The Unix Wars)

Durante los años 70, AT&T (dueña de Bell Labs) tenía prohibido por un decreto antimonopolio del gobierno de EE.UU. vender software o entrar en el negocio de la informática. Solo podían dedicarse a los teléfonos.
Así que, cuando las universidades pedían Unix, AT&T les enviaba las cintas magnéticas con el código fuente y decía: *"Toma, cobramos solo el coste de la cinta y el envío. Es gratis, pero no damos soporte. Si se rompe, arréglalo tú"*.

Esto creó una edad de oro de colaboración académica. La **Universidad de Berkeley (California)** se convirtió en un foco de desarrollo masivo.
Los estudiantes de Berkeley tomaron el código de Unix, lo mejoraron radicalmente y crearon su propia variante: **BSD (Berkeley Software Distribution)**.
Berkeley añadió cosas vitales:
*   La pila **TCP/IP** (la base de Internet).
*   El editor `vi`.
*   La shell `csh`.
*   La memoria virtual.

### El Cisma de 1984
En 1984, el gobierno de EE.UU. dividió a AT&T en empresas más pequeñas ("Baby Bells"). La restricción legal que les impedía vender software desapareció.
AT&T vio que Unix valía oro y decidió comercializarlo agresivamente. Crearon **System V**, una versión corporativa, cerrada y cara.
*   Cerraron el código fuente (secreto comercial).
*   Empezaron a cobrar licencias millonarias ($40,000 por CPU).
*   Prohibieron a las universidades compartir el código.
*   Demandaron a quienes copiaban su código.

El mundo de Unix se partió en una guerra civil sangrienta:
1.  **System V (AT&T):** El estándar corporativo.
2.  **BSD (Berkeley):** La versión académica y rebelde.

Fabricantes como HP, IBM, Sun Microsystems y Microsoft compraron licencias y crearon sus propios Unix incompatibles (HP-UX, AIX, Solaris, Xenix). El sueño de un sistema estándar se rompió. Un programa escrito para HP-UX no funcionaba en AIX. Esta fragmentación estancó la innovación y dejó el camino libre para que Windows conquistara el mercado.

@section: 6. Richard Stallman y la Cruzada Moral (GNU)

Mientras las empresas peleaban por el dinero, en el Laboratorio de Inteligencia Artificial del MIT, un hombre veía cómo su cultura hacker se desmoronaba. **Richard Stallman** (RMS) vio cómo sus colegas eran contratados por empresas que les obligaban a firmar acuerdos de confidencialidad (NDA). El software dejaba de ser conocimiento compartido para convertirse en propiedad privada.

### El Incidente de la Impresora Xerox
La leyenda cuenta que el punto de ruptura fue una impresora láser Xerox 9700. La impresora se atascaba constantemente. Stallman quería modificar el controlador (driver) para que la impresora enviara un mensaje a los usuarios avisando del atasco.
Pidió el código fuente a Xerox. Se lo negaron: *"Es secreto comercial"*.
Stallman visitó a un colega de otra universidad que tenía el código. Se lo pidió. El colega, avergonzado, dijo: *"No puedo dártelo. He firmado un NDA"*.

Para Stallman, esto no fue un inconveniente técnico; fue una traición ética. Decidió que el software privativo era inmoral porque dividía a la sociedad y mantenía a los usuarios indefensos.

### El Manifiesto GNU (1983)
El 27 de septiembre de 1983, Stallman renunció al MIT y anunció el proyecto **GNU** (GNU's Not Unix).
Su objetivo: Crear un sistema operativo completo, compatible con Unix, pero 100% libre.

Definió las **4 Libertades del Software**:
0.  Libertad de **usar** el programa con cualquier propósito.
1.  Libertad de **estudiar** cómo funciona y modificarlo (Acceso al Código Fuente).
2.  Libertad de **distribuir** copias a tus vecinos.
3.  Libertad de **mejorar** el programa y hacer públicas las mejoras.

Para proteger estas libertades, creó la licencia **GPL (Copyleft)**. A diferencia del Dominio Público, la GPL usa el copyright al revés: *"Tienes derecho a copiar, modificar y distribuir. PERO, si distribuyes una versión modificada, **tienes la obligación** de mantenerla libre y entregar el código fuente"*. Es un virus legal de libertad.

Durante los 80, Stallman y la FSF (Free Software Foundation) trabajaron frenéticamente. Crearon herramientas de calidad industrial que superaban a las de Unix propietario: **GCC** (compilador), **Bash** (shell), **Emacs** (editor), **Glibc** (librerías).
Para 1990, tenían TODO el sistema listo... excepto una pieza. El Kernel. Su propio kernel, **Hurd**, era demasiado complejo y no funcionaba.

@section: 7. El Estudiante Finlandés y su "Hobby" (1991)

En Helsinki, un estudiante de 21 años llamado **Linus Torvalds** se compró un PC nuevo con un procesador **Intel 386**. Este chip era especial: tenía capacidades avanzadas de gestión de memoria (modo protegido) que permitían la multitarea real.

Linus usaba **MINIX**, un sistema operativo tipo Unix creado por el profesor **Andrew Tanenbaum** para enseñar en clase. Pero MINIX era limitado deliberadamente (para que los alumnos pudieran entenderlo en un semestre). Linus quería explorar la potencia real de su 386.

Empezó escribiendo un emulador de terminal para conectarse a los servidores de la universidad. Para guardar archivos en su disco duro, tuvo que escribir un controlador de disco. Para gestionar la memoria, escribió un gestor de memoria. Sin darse cuenta, estaba escribiendo un Kernel.

El **25 de agosto de 1991**, envió su famoso correo a Usenet (`comp.os.minix`):

> *"Hola a todos los que usáis minix... Estoy haciendo un sistema operativo (gratis) (solo un hobby, no será grande y profesional como gnu) para clones AT 386(486)..."*

Lo llamó **Linux** (aunque él quería llamarlo *Freax*, el administrador del servidor FTP lo renombró).

### El Debate Tanenbaum-Torvalds (1992)
En 1992, el profesor Tanenbaum criticó públicamente a Linux en un debate legendario.
*   **Tanenbaum:** "Linux es obsoleto". Argumentaba que Linux usaba una arquitectura **Monolítica** (todo en un gran bloque de código), lo cual consideraba un paso atrás frente a los **Microkernels** (modernos, modulares y académicos).
*   **Linus:** Respondió con pragmatismo brutal. Dijo que, en teoría, los microkernels eran bonitos, pero en la práctica eran complejos y lentos. "Linux consigue hacer el trabajo".

Esta discusión cimentó la filosofía de Linux: **El pragmatismo sobre la pureza teórica.**

@section: 8. El Matrimonio de Conveniencia: GNU/Linux

Aquí ocurrió la reacción química.
1.  Linus tenía un **Kernel** funcional pero apenas tenía aplicaciones.
2.  Stallman tenía todas las aplicaciones (**GNU**) pero no tenía Kernel.

Los desarrolladores de todo el mundo descargaron el kernel de Linus (apenas unos KB), lo compilaron usando GCC (de GNU) y corrieron Bash (de GNU) sobre él.
¡CLACK! Las piezas encajaron perfectamente.

Nació un sistema operativo completo. Aunque comúnmente lo llamamos "Linux", el nombre técnicamente correcto (y por el que Stallman lucha incansablemente) es **GNU/Linux**, reconociendo que el sistema es la suma indisoluble de ambas partes.

### El Modelo del Bazar
Eric S. Raymond analizó por qué Linux triunfó donde otros (como BSD o Hurd) fallaron en su ensayo *"La Catedral y el Bazar"*.
*   **La Catedral (GNU Hurd, Unix comercial):** El software es construido por un grupo selecto de expertos en privado y se lanza al público cuando está "perfecto". Es lento y elitista.
*   **El Bazar (Linux):** Libera pronto, libera a menudo ("Release early, release often"). Delega todo lo que puedas. Trata a tus usuarios como co-desarrolladores. "Con suficientes ojos mirando, todos los errores son superficiales" (Ley de Linus).

Linux demostró que miles de voluntarios descoordinados podían producir mejor software que las empresas más grandes del mundo.

@section: 9. El Imperio Contraataca y la Victoria Final

En los 90, Linux creció exponencialmente. Nacieron las distribuciones (Slackware, Debian, Red Hat).
Empresas como **IBM**, **Oracle** y **HP** se dieron cuenta de que mantener sus propios Unix (AIX, Solaris) era carísimo. Era más barato invertir en Linux y compartir los costes de desarrollo del núcleo. En 2001, IBM invirtió 1.000 millones de dólares en Linux. Eso legitimó el sistema para los bancos y gobiernos.

### Los Documentos Halloween y SCO
Microsoft vio a Linux como una amenaza existencial. Se filtraron los "Documentos Halloween", memorandos internos donde ingenieros de Microsoft admitían que el Código Abierto era, a largo plazo, superior y que debían combatirlo con trucos sucios ("FUD" - Miedo, Incertidumbre y Duda). Steve Ballmer llamó a Linux "un cáncer".

Financiaron indirectamente a una empresa llamada **SCO** (Santa Cruz Operation) que demandó a IBM y a usuarios de Linux por miles de millones, alegando que Linux contenía código robado de Unix original.
Fue una batalla legal de años. La comunidad auditó el código línea por línea. No encontraron nada robado. SCO perdió, quebró, y Linux salió fortalecido legalmente como una roca.

### La Conquista Silenciosa
Hoy, la guerra ha terminado. Linux ha ganado, aunque no lo veas en tu escritorio.
*   **Internet:** Google, Facebook, Amazon, Netflix... el 99% de la nube corre sobre Linux.
*   **Móviles:** Android es un kernel Linux. Hay miles de millones de dispositivos en bolsillos de todo el mundo.
*   **Supercomputación:** El 100% de las 500 supercomputadoras más potentes del mundo usan Linux.
*   **Ciencia:** El Gran Colisionador de Hadrones, la NASA, SpaceX... todos usan Linux.
*   **Microsoft:** Hoy, Microsoft es "Platinum Member" de la Fundación Linux y vende Linux en su nube Azure.

Cuando usas Linux, no estás usando solo un programa. Estás usando el resultado de la colaboración humana más grande de la historia. Estás a hombros de gigantes.

@quiz: ¿Cuál fue la principal diferencia filosófica entre el desarrollo de Linux (el Bazar) y el de Unix comercial (la Catedral)?
@option: El Bazar usaba lenguajes más modernos.
@correct: El Bazar liberaba código constantemente y permitía que cualquiera contribuyera, mientras que la Catedral era desarrollo cerrado.
@option: La Catedral era más rápida corrigiendo errores.

@quiz: ¿Qué evento legal en los años 80 provocó la fragmentación de Unix y el cierre de su código fuente?
@option: La invención del lenguaje C.
@option: La demanda de SCO.
@correct: La división de AT&T por el gobierno de EE.UU., permitiéndole comercializar software.
@option: La creación de Microsoft.

@quiz: ¿Qué componente vital aportó el proyecto GNU para completar el sistema operativo que inició Linus Torvalds?
@option: El Kernel.
@option: Los drivers de tarjeta gráfica.
@correct: El compilador (GCC), la shell (Bash) y las herramientas de usuario.
@option: El entorno de escritorio Windows.

@quiz: Según el profesor Tanenbaum, ¿por qué Linux era "obsoleto" en 1992?
@option: Porque estaba escrito en C.
@correct: Porque usaba una arquitectura de Kernel Monolítico en lugar de Microkernel.
@option: Porque era gratuito.
