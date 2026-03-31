
@title: Examen: Operador Linux Jr.
@exam
@icon: ⚔️
@description: Examen tipo test sobre los cuatro módulos del curso; superar la mayoría de preguntas para la certificación Operador Linux Jr.
@order: 5

# Examen de certificación: Operador Linux Jr.

Has recorrido un largo camino. Desde entender qué es el Kernel hasta escribir tus propios scripts, pasando por la gestión de permisos y procesos.

Ha llegado el momento de probar tu valía. Este examen cubre todos los conceptos vitales aprendidos en los 4 módulos anteriores.

> **Instrucciones:** Selecciona la única respuesta correcta para cada pregunta. Debes acertar la mayoría para superar la prueba y obtener la certificación del nodo.


## Bloque 1: Arquitectura y Fundamentos

@quiz: Técnicamente hablando, ¿qué es "Linux"?
@option: Un sistema operativo completo con entorno gráfico.
@correct: El Kernel (núcleo) del sistema operativo.
@option: Una distribución de software libre.
@option: Un entorno de escritorio similar a Windows.

@quiz: ¿Qué es una "Distribución" (Distro) de Linux?
@option: Una versión pirata de Windows.
@option: El código fuente del Kernel sin compilar.
@correct: Un conjunto que incluye el Kernel, herramientas GNU, entorno gráfico y gestor de paquetes.
@option: Un programa para distribuir archivos en red.

@quiz: En la estructura de directorios de Linux, ¿cuál es el símbolo que representa la raíz del sistema de archivos?
@option: \ (Barra invertida)
@option: C:
@correct: / (Barra normal)
@option: ~ (Virgulilla)

@quiz: ¿Qué característica define a la carpeta `/tmp`?
@option: Guarda los archivos temporales para siempre.
@correct: Su contenido se borra automáticamente al reiniciar el sistema.
@option: Solo el usuario root puede escribir en ella.
@option: Contiene los drivers temporales del hardware.

@quiz: ¿En qué directorio se almacenan los archivos de configuración del sistema global?
@option: /home
@option: /bin
@correct: /etc
@option: /var

@quiz: ¿Qué directorio contiene archivos especiales que representan dispositivos de hardware (como discos duros)?
@option: /dev
@correct: /dev
@option: /mnt
@option: /media
@option: /sys

@quiz: ¿Cuál es el proceso con PID 1, encargado de arrancar el resto de servicios en sistemas modernos?
@option: bash
@option: kernel
@correct: systemd (o init)
@option: grub

@quiz: ¿Qué es el "Espacio de Usuario" (User Space)?
@option: La carpeta /home donde viven los usuarios.
@option: La memoria RAM reservada para la tarjeta gráfica.
@correct: La zona de memoria donde se ejecutan las aplicaciones normales, sin acceso directo al hardware.
@option: El espacio libre en el disco duro.

@quiz: ¿Qué función cumple la partición o archivo SWAP?
@option: Almacenar el arranque del sistema (Bootloader).
@correct: Actuar como memoria virtual cuando la RAM se llena.
@option: Guardar copias de seguridad automáticas.
@option: Acelerar la conexión a internet.

@quiz: ¿Qué significa que un software sea "Open Source"?
@option: Que es gratis (gratis como en cerveza).
@correct: Que su código fuente es accesible, modificable y redistribuible (libre como en libertad).
@option: Que no tiene derechos de autor.
@option: Que solo funciona en sistemas Linux.


## Bloque 2: La Terminal y Archivos

@quiz: Estás en `/home/usuario/documentos` y quieres subir un nivel hacia `/home/usuario`. ¿Qué comando usas?
@option: cd .
@correct: cd ..
@option: cd /
@option: cd ~

@quiz: ¿Qué hace el comando `pwd`?
@option: Cambia tu contraseña (password).
@correct: Muestra la ruta completa del directorio donde estás (Print Working Directory).
@option: Muestra los procesos en ejecución.
@option: Apaga el ordenador (Power Down).

@quiz: ¿Qué comando utilizarías para renombrar el archivo `foto.jpg` a `imagen.jpg`?
@option: cp foto.jpg imagen.jpg
@option: ren foto.jpg imagen.jpg
@correct: mv foto.jpg imagen.jpg
@option: rm foto.jpg imagen.jpg

@quiz: ¿Para qué sirve el comando `touch` si el archivo ya existe?
@option: Borra el contenido del archivo.
@correct: Actualiza la fecha de modificación del archivo al momento actual.
@option: Crea una copia del archivo.
@option: Abre el archivo para editarlo.

@quiz: Has ejecutado `ls` y no ves los archivos que empiezan por un punto (ej: `.bashrc`). ¿Qué opción necesitas?
@option: ls -l
@correct: ls -a
@option: ls -h
@option: ls -R

@quiz: ¿Cómo crearías una estructura de directorios anidada `proyecto/src/img` con un solo comando?
@option: mkdir proyecto/src/img
@correct: mkdir -p proyecto/src/img
@option: mkdir -r proyecto/src/img
@option: touch proyecto/src/img

@quiz: ¿Qué comando es extremadamente peligroso y borrará todo sin preguntar ni posibilidad de recuperación?
@option: rm -i archivo
@option: rmdir carpeta
@correct: rm -rf /
@option: delete all

@quiz: Necesitas saber qué tipo de contenido tiene un archivo llamado `data` que no tiene extensión. ¿Qué usas?
@option: cat data
@option: ls -l data
@correct: file data
@option: type data

@quiz: Necesitas encontrar todos los archivos que terminen en `.conf` dentro de la carpeta `/etc`.
@option: grep -r ".conf" /etc
@correct: find /etc -name "*.conf"
@option: locate /etc
@option: search .conf

@quiz: ¿Cuál es la diferencia principal entre `cat` y `less`?
@option: `cat` es para editar y `less` para leer.
@correct: `cat` muestra todo de golpe, `less` permite navegar por el archivo paginando.
@option: `less` es una versión antigua de `cat`.
@option: `cat` solo funciona con archivos pequeños.


## Bloque 3: Administración y Permisos

@quiz: Si un archivo tiene permisos `755` (rwxr-xr-x), ¿qué puede hacer el "Grupo"?
@option: Leer, Escribir y Ejecutar.
@correct: Leer y Ejecutar, pero no Escribir.
@option: Solo Leer.
@option: Nada.

@quiz: ¿Qué valor numérico (octal) representa el permiso de "Lectura y Escritura" (rw-)?
@option: 7
@correct: 6
@option: 5
@option: 4

@quiz: ¿Qué comando utilizas para cambiar el propietario de un archivo?
@option: chmod
@correct: chown
@option: chgrp
@option: passwd

@quiz: ¿Para qué sirve el comando `sudo`?
@option: Para cambiar la contraseña de un usuario.
@correct: Para ejecutar un comando con privilegios de administrador (root).
@option: Para suspender el ordenador.
@option: Para desinstalar programas.

@quiz: ¿En qué archivo se almacenan las contraseñas de los usuarios de forma cifrada?
@option: /etc/passwd
@option: /etc/security
@correct: /etc/shadow
@option: /var/log/auth.log

@quiz: ¿Cuál es el UID (User ID) del usuario root?
@option: 1000
@option: 1
@correct: 0
@option: -1

@quiz: Tienes un proceso bloqueado que no responde a un `kill` normal. ¿Qué señal usas para forzar su destrucción inmediata?
@option: -15 (SIGTERM)
@option: -1 (SIGHUP)
@correct: -9 (SIGKILL)
@option: -2 (SIGINT)

@quiz: ¿Qué comando te dice qué usuario eres actualmente en la terminal?
@option: whoareyou
@correct: whoami
@option: pwd
@option: w

@quiz: ¿Qué comando te permite ver los procesos y el consumo de recursos en tiempo real?
@option: ps aux
@correct: top (o htop)
@option: df -h
@option: free -m

@quiz: ¿Qué archivo debes editar (usando `visudo`) para configurar quién puede usar `sudo`?
@option: /etc/passwd
@option: /etc/admin
@correct: /etc/sudoers
@option: /etc/group


## Bloque 4: Redes y Scripting

@quiz: ¿Qué comando usarías para ver tu dirección IP asignada?
@option: ip address (o ip addr)
@option: ping localhost
@option: netstat
@correct: ip addr (o ip a)

@quiz: Si haces `ping google.com` y falla, pero `ping 8.8.8.8` funciona, ¿qué está fallando?
@option: Tu tarjeta de red.
@correct: El servidor DNS (Resolución de nombres).
@option: El cable de red.
@option: El servidor de Google.

@quiz: Quieres instalar el paquete `git` en un sistema Ubuntu/Debian. ¿Cuál es el primer paso recomendado antes de instalar?
@correct: sudo apt update
@option: sudo apt install git
@option: sudo apt upgrade
@option: sudo apt remove git

@quiz: ¿Qué hace el comando `grep`?
@option: Descarga archivos de internet.
@correct: Busca texto o patrones dentro de archivos o flujos de datos.
@option: Agrupa archivos en carpetas.
@option: Muestra el uso de la memoria RAM.

@quiz: ¿Qué significa la primera línea de un script: `#!/bin/bash`?
@option: Es un comentario y se ignora.
@correct: Es el Shebang, indica qué intérprete debe ejecutar el script.
@option: Define la ruta donde se guardará el script.
@option: Da permisos de ejecución al script.

@quiz: Para ejecutar un script llamado `mi_script.sh` en la carpeta actual, ¿qué comando es el correcto?
@option: mi_script.sh
@correct: ./mi_script.sh
@option: run mi_script.sh
@option: call mi_script.sh

@quiz: En un script, ¿cómo accedes al valor de una variable llamada `NOMBRE`?
@option: NOMBRE
@correct: $NOMBRE
@option: %NOMBRE%
@option: &NOMBRE

@quiz: ¿Qué código de salida (Exit Code) indica que un comando se ejecutó con éxito?
@option: 1
@option: -1
@correct: 0
@option: 100

@quiz: ¿Qué operador de redirección se usa para añadir texto al final de un archivo sin borrar su contenido actual?
@option: >
@correct: >>
@option: <
@option: |

@quiz: ¿Cuál es la combinación de teclas para detener (matar) un programa que se está ejecutando en la terminal (SIGINT)?
@option: Ctrl + Z
@correct: Ctrl + C
@option: Ctrl + D
@option: Alt + F4
