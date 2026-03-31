@title: Servicios de Impresión y Gestión de Drivers
@icon: 🖨️
@description: Centralizando la gestión de impresoras en la empresa.
@order: 5

# Servicios de impresión y gestión de drivers

El rol **Print and Document Services** centraliza **colas**, **drivers** y **permisos** en Windows Server. En entornos educativos y oficinas, reduce visitas a puesto al permitir **despliegue por GPO** y **impresión por ubicación**.

@section: Objetivos didácticos

*   Instalar el rol de **impresión** y compartir una cola.
*   **Empaquetar drivers** x64/x86 para clientes.
*   Publicar impresoras en AD y **asignarlas por GPO** (opcional).

@section: Instalación

Server Manager → **Print and Document Services** → **Print Server**.

Crea una **impresora** apuntando a:

*   **TCP/IP** (impresora de red con IP fija).
*   **WSD** (Web Services for Devices) en algunos modelos.

@section: Drivers

*   **Driver del servidor:** debe coincidir con la arquitectura del cliente que se conecta (x64 predominante).
*   **Instalación adicional de drivers:** pestaña **Drivers** → añade **x86** si aún hay clientes legacy.

**Point and Print** ha sido restringido por seguridad en versiones recientes; revisa **políticas de grupo** y KB de Windows sobre restricciones de drivers.

@section: Permisos

*   **Permissions** en la cola: `Print`, `Manage Documents`, `Manage this printer`.
*   **Auditoría** de trabajos para departamentos sensibles.

@section: Branch Office (opcional)

**Branch Office Direct Printing** (donde aplique) reduce tráfico a través del servidor central.

@section: Despliegue

1.  **List in directory:** publica en AD.
2.  **GPO:** **User Configuration → Preferences → Control Panel Settings → Printers** o **Deployed Printers**.

@section: Práctica

1.  Comparte una cola `\\srv-print\HP-Lab`.
2.  Conecta desde cliente y verifica el **nombre del driver** instalado.
3.  Documenta un **problema típico:** driver incorrecto → cola atascada en **error**.

@quiz: ¿Qué rol de Windows Server instala el servicio de colas de impresión compartidas?
@option: IIS
@correct: Print and Document Services
@option: WDS

@quiz: ¿Por qué se deben instalar drivers adicionales (x86/x64) en el servidor de impresión?
@correct: Para que los clientes distintos arquitectura puedan descargar el driver correcto al conectarse
@option: Para acelerar el procesador del servidor
@option: Solo por estética
