@title: Objetos de Directiva de Grupo (GPO): Fundamentos
@icon: 🏛️
@description: La herramienta más potente para configurar masivamente equipos Windows.
@order: 1

# GPO: fundamentos (instituto / administración de sistemas)

Las **directivas de grupo (GPO)** aplican configuración a **usuarios** y **equipos** en sitios, dominios u **OUs**. Son el mecanismo estándar para seguridad, software, scripts de inicio y preferencias. Dominar **herencia**, **filtrado de seguridad** y **orden de vínculo** es imprescindible.

@section: Objetivos didácticos

*   Crear y vincular una GPO a una OU.
*   Explicar **herencia**, **No Override** y **Block Inheritance**.
*   Usar **GPMC** (`gpmc.msc`) para informes y **Resultant Set of Policy (RSoP)**.

@section: Estructura de una GPO

Secciones típicas:

*   **Computer Configuration** → **Policies** → Windows, seguridad, software…
*   **User Configuration** → idem.

**Preferences** (preferencias) ≠ **Policies** (políticas): las preferencias pueden ser “suaves” y no siempre bloquean el cambio local.

@section: Vínculo y alcance

*   Una GPO se **vincula** a un contenedor (sitio, dominio, OU).
*   **Orden de procesamiento:** local → sitio → dominio → OUs (de padre a hijo); **la última GPO gana** en conflicto (salvo excepciones de bloqueo).

@section: Filtrado de seguridad

Por defecto **Authenticated Users** puede leer y aplicar. Restringe a **grupos** específicos cuando una GPO es solo para un subconjunto de equipos.

**WM Filter** (siguiente lección avanzada) añade condiciones por hardware/OS.

@section: Computer vs User

*   Políticas de **equipo** suelen procesarse al **arranque** (`gpupdate /force`).
*   Políticas de **usuario** al **inicio de sesión** o intervalo de fondo.

@section: Herramientas

*   **Group Policy Management Console (GPMC)**.
*   `gpresult /h report.html` en el cliente.
*   **Group Policy Modeling** para simular el efecto antes de desplegar.

@section: Buenas prácticas

*   Nombres descriptivos: `GPO-Security-Baseline-Workstations`.
*   Una GPO por **área lógica** (seguridad, Office, impresoras) facilita troubleshooting.
*   **Backup** de GPOs desde GPMC antes de cambios masivos.

@section: Práctica

1.  Crea `GPO-Lab-Wallpaper` (o una política de seguridad simple como timeout de pantalla).
2.  Vincúla a una OU de prueba.
3.  Ejecuta `gpupdate /force` y verifica con `gpresult`.

@quiz: ¿Qué consola administrativa se usa para crear, vincular y hacer backup de GPOs?
@option: dnsmgmt.msc
@correct: gpmc.msc (Group Policy Management)
@option: lusrmgr.msc

@quiz: En caso de conflicto entre dos GPOs vinculadas a la misma OU, ¿qué factor determina normalmente la precedencia?
@option: El orden alfabético del nombre
@correct: El orden de vínculo (link order) y si hay bloqueo de herencia / No Override
@option: La fecha de instalación de Windows
