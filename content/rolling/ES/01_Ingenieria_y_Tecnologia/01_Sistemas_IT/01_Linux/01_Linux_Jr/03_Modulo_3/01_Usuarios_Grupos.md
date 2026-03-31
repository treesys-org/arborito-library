@title: Usuarios y grupos locales: passwd, shadow, sudo
@icon: 👥
@description: Cuentas locales: /etc/passwd y shadow, grupos, herramientas useradd/usermod y uso prudente de sudo (enfoque LPIC-1).
@order: 1

# Usuarios y grupos en Linux: cuentas locales y privilegios

Esta lección cubre el **modelo multiusuario**: archivos como `/etc/passwd` y `/etc/shadow`, grupos, creación y modificación de cuentas (`useradd`, `usermod`…) y un uso consciente de **sudo** y PAM, base para administración y para el examen LPIC-1.

@section: Mapa LPIC-1 — Módulo 3 (cuentas, permisos, procesos, archivos)

Cobertura orientada a **LPIC-1 (107 / 103 / 104)**:

*   **107.1 Administración de usuarios y grupos locales:** `/etc/passwd`, `/etc/shadow`, `/etc/group`, `useradd`, `usermod`, `groupadd`, contraseñas (`passwd`, `chage`).
*   **107.2 automatización y tareas programadas:** base para `cron`/`at` (profundidad operativa en scripting y en avanzado); aquí privilegios y cuentas de servicio.
*   **107.3 localización e internacionalización:** referencia cruzada con variables `LANG`/`LC_*` cuando afecte a salida de comandos.
*   **104.5 permisos estándar y especiales:** `chmod`, `chown`, `umask` (lección dedicada).
*   **103.4 procesos:** `ps`, `top`, señales, prioridades (módulo 3).
*   **Seguridad local:** `sudo`, PAM (introducido aquí; SELinux/AppArmor en curso avanzado).
*   **RHEL:** `useradd` defaults en `/etc/default/useradd`; **Debian:** adduser amigable; ambos deben conocerse en entrevistas.

Bienvenido al Módulo 3. Aquí es donde dejas de ser un simple usuario y te conviertes en **Administrador**.

Si vienes de Windows (versiones domésticas), probablemente estás acostumbrado a que tu PC sea "tuyo". Lo enciendes, entras, y haces lo que quieres. Eres el dueño.
En Linux (y Unix), la filosofía es radicalmente distinta. Linux se diseñó en los años 70 para mainframes: ordenadores gigantescos y carísimos que tenían que ser compartidos por cientos de personas a la vez.

Imagina que Linux no es una casa unifamiliar, sino un **Rascacielos Corporativo**.
*   Tiene que haber un **Conserje Supremo** con llaves de todo (Root).
*   Hay **Empleados** que tienen llave de su despacho pero no del despacho del jefe (Usuarios).
*   Hay **Personal de Limpieza** que tiene llaves de los pasillos pero no de las cajas fuertes (Grupos).
*   Hay **Robots** de mantenimiento que trabajan en los sótanos (Usuarios de Sistema).

En esta lección masiva, vamos a aprender a gestionar a todos los habitantes de este rascacielos. Vamos a crear vidas digitales, vamos a agruparlas, vamos a darles poderes y, finalmente, vamos a aprender a eliminarlas sin dejar rastro.

@section: 1. Los Actores del Sistema

Antes de teclear comandos, necesitamos entender las castas sociales de Linux.

### 1.1 El Dios: Root (UID 0)
En Linux, hay una cuenta especial llamada `root`.
`root` no es solo un administrador como en Windows. `root` es Dios.
*   Puede leer cualquier archivo (incluso los privados de otros).
*   Puede borrar cualquier cosa (incluso el propio sistema operativo mientras está funcionando).
*   No obedece a las reglas de permisos.

El sistema identifica a `root` no por su nombre, sino por su DNI número 0 (UID 0). Cualquier usuario con UID 0 es Dios.

### 1.2 Los Ciudadanos: Usuarios Normales (UID 1000+)
Son los humanos. Tú, yo, tus compañeros de trabajo.
*   Viven en `/home/nombre_usuario`.
*   Solo tienen poder dentro de su propia casa. No pueden instalar programas para todos, ni cambiar la hora del sistema, ni ver los archivos de otros usuarios.
*   Normalmente empiezan a contar desde el UID 1000 en la mayoría de distros (Ubuntu, Fedora).

### 1.3 Los Invisibles: Usuarios de Sistema (UID 1-999)
Esto confunde mucho a los novatos. Si haces un `cat /etc/passwd`, verás decenas de usuarios que no conoces: `www-data`, `lp`, `mail`, `nobody`, `daemon`, `sshd`.
¿Te han hackeado? **No.**

Son cuentas creadas para que los **programas** (servicios) se ejecuten.
*   **Seguridad:** El servidor web (Apache o Nginx) no se ejecuta como `root`. Se ejecuta como el usuario `www-data`. Si un hacker rompe tu servidor web, solo consigue los poderes de `www-data`, que son muy limitados. No consigue el control total del sistema.
*   Estos usuarios **no tienen contraseña** y **no pueden iniciar sesión** (no tienen shell). Son robots trabajadores.

@section: 2. Los Tres Libros Sagrados

En Windows, los usuarios se guardan en una base de datos binaria oculta y compleja (SAM).
En Linux, como todo, **son archivos de texto plano**. Puedes leerlos. Debes entenderlos.

Estos tres archivos en la carpeta `/etc` son el corazón de la identidad del sistema.

### Libro 1: `/etc/passwd` (El Censo)
Este archivo contiene la lista de todos los usuarios. Es público (cualquiera puede leerlo, `cat /etc/passwd`).
Cada línea representa un usuario y tiene 7 campos separados por dos puntos `:`.

**Ejemplo:**
`juan:x:1001:1001:Juan Perez,RRHH,,:/home/juan:/bin/bash`

Vamos a diseccionarlo:
1.  **Username (`juan`):** El nombre para loguearse.
2.  **Password (`x`):** Antiguamente aquí iba la contraseña cifrada. Hoy en día, la `x` significa "La contraseña está guardada de forma segura en `/etc/shadow`, no aquí".
3.  **UID (`1001`):** User ID. El número real que usa el Kernel para identificar al usuario.
4.  **GID (`1001`):** Group ID. El grupo principal al que pertenece Juan.
5.  **GECOS (`Juan Perez,RRHH,,`):** Información extra (Nombre completo, departamento, teléfono). Es opcional y puramente informativo.
6.  **Home (`/home/juan`):** Dónde aterriza el usuario al entrar.
7.  **Shell (`/bin/bash`):** Qué programa se ejecuta cuando entra. Si pones `/bin/false` o `/sbin/nologin` aquí, le prohíbes la entrada al sistema.

### Libro 2: `/etc/shadow` (Los Secretos)
Este archivo contiene las contraseñas. **Solo root puede leerlo.** Es la cámara acorazada.

**Ejemplo:**
`juan:$6$xyz...:19850:0:99999:7:::`

Disección rápida:
1.  **Username:** Juan.
2.  **Hash ($6$xyz...):** La contraseña cifrada. Si ves `*` o `!`, la cuenta está bloqueada (no tiene password).
3.  **Last Change:** Días desde 1970 que se cambió la password.
4.  **Min Days:** Días mínimos antes de poder cambiarla de nuevo.
5.  **Max Days:** Días antes de que caduque (política de seguridad).
6.  **Warning:** Días de aviso antes de caducar.

### Libro 3: `/etc/group` (Las Cofradías)
Define los grupos.
`desarrolladores:x:2000:juan,maria,pedro`

1.  **Nombre del grupo:** desarrolladores.
2.  **Password:** (Casi nunca se usa, `x`).
3.  **GID:** El ID numérico del grupo.
4.  **Miembros:** Lista de usuarios que pertenecen a este grupo *secundario*.

@quiz: ¿Por qué aparece una 'x' en el campo de contraseña del archivo `/etc/passwd`?
@option: Porque el usuario no tiene contraseña.
@correct: Porque la contraseña real (cifrada) se ha movido a `/etc/shadow` por seguridad.
@option: Porque el usuario está bloqueado.
@option: Es un error del sistema.

@section: 3. Creando Vida: `useradd` y `adduser`

Para crear un usuario, no editamos los archivos a mano (demasiado riesgo de error). Usamos comandos.
Aquí surge una duda eterna: ¿Uso `useradd` o `adduser`?

### La Herramienta Cruda: `useradd`
Es el comando estándar, universal en todos los Linux. Es un binario de bajo nivel.
**El problema:** Es tonto. Por defecto, crea el usuario pero **NO** crea su carpeta `/home`, ni le pone contraseña, ni le asigna una shell decente (a veces pone `sh` en lugar de `bash`).

Si usas `useradd`, tienes que darle instrucciones precisas:
```bash
# Crear a Pedro, creando su home (-m), especificando su shell (-s) y un comentario (-c)
$ sudo useradd -m -s /bin/bash -c "Pedro del Taller" pedro
```
Si olvidas el `-m`, Pedro existirá, pero no tendrá casa. Al loguearse dará error.

### La Herramienta Amigable: `adduser`
Es un script (habitual en Debian/Ubuntu) que usa `useradd` por debajo pero te hace preguntas interactivas.
```bash
$ sudo adduser maria
```
El sistema te preguntará:
1.  Contraseña para Maria.
2.  Nombre completo.
3.  Número de habitación.
4.  Teléfono.
Y automáticamente creará el `/home`, configurará los permisos y lo dejará todo listo.
**Recomendación:** Si eres humano, usa `adduser`. Si estás haciendo un script automático, usa `useradd`.

### Asignando Contraseña: `passwd`
Si usaste `useradd`, el usuario existe pero no tiene contraseña (está bloqueado). Tienes que dársela.
```bash
$ sudo passwd pedro
```
También puedes cambiar tu propia contraseña simplemente escribiendo `passwd`.

**Truco de Seguridad:**
Puedes bloquear una cuenta temporalmente (por ejemplo, empleado de vacaciones) sin borrarla.
*   Bloquear: `sudo passwd -l pedro` (Lock).
*   Desbloquear: `sudo passwd -u pedro` (Unlock).

@section: 4. Cirugía de Usuarios: `usermod`

La gente cambia. Se mudan de departamento, cambian de apellido o necesitan nuevos permisos. `usermod` permite editar una cuenta existente.

### Añadir a un Grupo (El comando más usado)
Imagina que quieres que Pedro pueda usar Docker. Tienes que añadirlo al grupo `docker`.
El error más común es usar `-G` (Groups) sin `-a` (Append/Añadir). Si haces eso, **borras a Pedro de todos sus otros grupos** y lo metes solo en Docker.

**Sintaxis Correcta (Tatuátela):**
`usermod -aG [GRUPO] [USUARIO]`
*   `-a`: Append (Añadir a lo que ya tiene).
*   `-G`: Grupos secundarios.

```bash
$ sudo usermod -aG sudo,docker pedro
```

### Otras operaciones comunes
*   **Cambiar el nombre de usuario (Login):**
    `sudo usermod -l nuevo_nombre viejo_nombre`
*   **Cambiar la carpeta Home:**
    `sudo usermod -d /home/nueva_casa -m pedro`
    *(La opción `-m` es vital: mueve los archivos de la casa vieja a la nueva. Sin ella, solo cambia la configuración pero los archivos se quedan tirados).*
*   **Cambiar la Shell:**
    `sudo usermod -s /bin/zsh pedro`

@section: 5. El Verdugo: `userdel`

Cuando un empleado se va, hay que eliminar su cuenta.
```bash
$ sudo userdel pedro
```
**¡Cuidado!** Esto borra al usuario del censo (`/etc/passwd`), pero **deja su carpeta `/home/pedro` intacta** en el disco duro. Esto se hace por seguridad, para no perder sus documentos.

Si quieres borrar al usuario Y sus archivos (borrado completo), usa `-r` (remove home):
```bash
$ sudo userdel -r pedro
```

@section: 6. Gestión de Grupos

Los grupos son la forma de organizar permisos colectivos.
Imagina una carpeta `/srv/proyecto` donde tienen que trabajar 5 programadores.
No vas a darle permisos a cada uno.
1.  Creas el grupo: `sudo groupadd programadores`.
2.  Añades a los usuarios: `sudo usermod -aG programadores ana`.
3.  Cambias el grupo dueño de la carpeta: `sudo chown :programadores /srv/proyecto`.
4.  Das permisos al grupo: `sudo chmod g+w /srv/proyecto`.

¡Listo! Ahora cualquiera en ese grupo puede trabajar ahí.

**Comandos de Grupo:**
*   `groupadd`: Crear grupo.
*   `groupdel`: Borrar grupo.
*   `groups [usuario]`: Ver a qué grupos pertenece un usuario.
*   `id [usuario]`: Información técnica completa (UID, GID, Grupos).

@quiz: ¿Qué opción es crítica al usar `usermod` para añadir un usuario a un grupo secundario sin sacarlo de los grupos a los que ya pertenece?
@option: -G
@correct: -a
@option: -u
@option: -A

@section: 7. SUDO: El Anillo de Poder

En los viejos tiempos, para hacer tareas administrativas, tenías que hacer esto:
1.  Escribir `su` (Switch User).
2.  Poner la contraseña de Root.
3.  Te convertías en Dios.
4.  Hacías el trabajo.
5.  Salías.

Esto es peligroso. Si te dejas la sesión de root abierta, o si ejecutas un virus siendo root, game over. Además, tenías que compartir la contraseña de root con todos los administradores.

**La solución: SUDO (SuperUser DO)**
Sudo permite a un usuario normal "pedir prestados" los poderes de root para **un solo comando**, usando **su propia contraseña**.

### Ventajas de Sudo
1.  **Responsabilidad:** Todo lo que se hace con sudo queda registrado en los logs (`/var/log/auth.log`). Sabemos *quién* rompió el servidor.
2.  **Seguridad:** No compartes la clave de root.
3.  **Granularidad:** Puedes configurar sudo para que Juan solo pueda ejecutar `apt update` y nada más.

### El Archivo `/etc/sudoers`
Este archivo define quién puede usar sudo y qué puede hacer.
**NUNCA** edites este archivo con un editor normal (`nano` o `vi`). Si cometes un error de sintaxis, **romperás sudo** y nadie podrá arreglarlo porque necesitas sudo para arreglarlo.

**Usa siempre:**
```bash
$ sudo visudo
```
Este comando abre el archivo, te deja editarlo, y **verifica la sintaxis antes de guardar**. Si hay un error, no guarda, salvándote la vida.

**Configuración típica:**
Para dar permisos totales a un usuario, normalmente lo añadimos al grupo `sudo` (en Debian/Ubuntu) o `wheel` (en RedHat/Fedora).
```bash
# Dar poderes de administrador a Ana
$ sudo usermod -aG sudo ana
```

@section: 8. Laboratorio Práctico: Contratando a un Becario

Vamos a simular un escenario real. Llega "Luis", el nuevo becario de desarrollo.
Objetivos:
1.  Crear su usuario.
2.  Asignarle el grupo `developers`.
3.  No darle permisos de `sudo` (es becario, no queremos que rompa nada).
4.  Obligarle a cambiar su contraseña la primera vez que entre.

**Paso 1: Crear el grupo de desarrolladores**
```bash
$ sudo groupadd developers
```

**Paso 2: Crear el usuario**
```bash
$ sudo useradd -m -s /bin/bash -c "Luis Becario" -G developers luis
```
*(Fíjate que uso -G mayúscula para añadirlo al grupo secundario directamente al nacer).*

**Paso 3: Asignar contraseña temporal**
```bash
$ sudo passwd luis
(Escribes: bienvenido123)
```

**Paso 4: Forzar cambio de contraseña (Expire)**
```bash
$ sudo chage -d 0 luis
```
*(`chage` gestiona la caducidad. `-d 0` pone la fecha del último cambio al día 0 (1970), lo que hace que el sistema piense que la contraseña está caducada inmediatamente).*

**Paso 5: Verificación**
```bash
$ id luis
uid=1002(luis) gid=1002(luis) groups=1002(luis),1003(developers)
```
Vemos que tiene su grupo principal (luis) y el secundario (developers). No está en el grupo `sudo`. ¡Perfecto!

@section: Resumen / Cheat Sheet

| Acción | Comando | Notas |
| :--- | :--- | :--- |
| **Crear Usuario** | `useradd -m -s /bin/bash usuario` | Usa `-m` para crear HOME. |
| **Crear Usuario (Fácil)** | `adduser usuario` | Interactivo (Ubuntu/Debian). |
| **Borrar Usuario** | `userdel -r usuario` | Usa `-r` para borrar HOME. |
| **Cambiar Password** | `passwd usuario` | `-l` para bloquear, `-u` desbloquear. |
| **Modificar Usuario** | `usermod` | `-aG` para añadir grupos. |
| **Crear Grupo** | `groupadd grupo` | |
| **Ver Info** | `id usuario` | Muestra UID, GID y grupos. |
| **Editar Sudoers** | `visudo` | **NUNCA** uses nano directo. |

Dominar los usuarios es la base de la seguridad en Linux. Recuerda: El principio del menor privilegio (Least Privilege). Dale a cada usuario solo el poder que necesita, ni un bit más.