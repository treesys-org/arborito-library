@title: WSUS: Gestión de Parches y Actualizaciones
@icon: 🩹
@description: Controlando qué actualizaciones de Windows se instalan y cuándo.
@order: 3

# WSUS: servidor de actualizaciones locales

**WSUS (Windows Server Update Services)** descarga actualizaciones desde Microsoft y las **apruebas** antes de desplegarlas a clientes y servidores. Es el estándar en muchas PYMEs que no pueden permitirse que cada equipo baje directamente de Internet sin control.

@section: Objetivos didácticos

*   Instalar WSUS y elegir **base de datos** (WID interna vs SQL Server).
*   Configurar **grupos de equipos**, **aprobaciones** y **ventanas de mantenimiento**.
*   Integrar con **GPO** para apuntar clientes al WSUS interno.

@section: Instalación

Rol **Windows Server Update Services**. Asistente:

1.  **Ubicación de contenido** (disco con espacio suficiente: los paquetes acumulan cientos de GB en entornos amplios).
2.  **Sincronización** inicial con Microsoft Update (programar horarios fuera de punta).
3.  **Productos y clasificaciones** (Windows 10/11, Server, Office, Defender).

@section: Aprobación

*   **Críticos/seguridad** suelen aprobarse rápido.
*   **Drivers** opcionales: con cuidado en estaciones de trabajo sensibles.

**Entornos de prueba:** grupo piloto → validación → aprobación masiva.

@section: GPO del cliente

Ruta típica: **Computer Configuration → Policies → Administrative Templates → Windows Components → Windows Update**.

*   **Specify intranet Microsoft update service location:** URL `http://servidor-wsus:8530`.
*   **Automatic Updates detection frequency.**
*   **No auto-restart** durante horas laborales (según política).

@section: Informes

Consola WSUS → **Reports** de cumplimiento, equipos **no** contactados, actualizaciones **needed**.

@section: Mantenimiento

*   **Limpieza** del servidor (`Server Cleanup Wizard`) para borrar revisiones sustituidas.
*   **Backup** de la base de datos si usas SQL externo.
*   **TLS/SSL** si expones la consola o IIS para reporting.

@section: Alternativas en la nube

Microsoft Endpoint Configuration Manager (ConfigMgr) y **cloud** (Update Rings en Intune) compiten/sustituyen a WSUS en empresas grandes.

@section: Práctica

1.  Aprueba una actualización menor a un grupo **Pilot**.
2.  Verifica en el cliente `wuauclt /reportnow` (según versión) o **Configuración → Windows Update** el estado.

@quiz: ¿Qué ventaja principal ofrece WSUS frente a que cada PC descargue directamente de Windows Update?
@option: Hace los PCs más rápidos automáticamente
@correct: Control centralizado de qué actualizaciones se aprueban y cuándo se despliegan
@option: Elimina la necesidad de antivirus

@quiz: ¿Qué política de grupo suele configurarse para que los clientes usen un servidor WSUS interno?
@option: Map network drive
@correct: Specify intranet Microsoft update service location
@option: Restricted groups
