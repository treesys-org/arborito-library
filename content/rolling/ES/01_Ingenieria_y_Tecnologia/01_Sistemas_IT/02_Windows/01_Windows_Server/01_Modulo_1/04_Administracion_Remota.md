@title: Administración Remota: RSAT y Windows Admin Center
@icon: 📡
@description: Gestionando tus servidores sin estar físicamente frente a ellos.
@order: 4

# Administración remota: RSAT y Windows Admin Center

En la práctica profesional, **no** administras servidores sentado frente a cada rack: usas **herramientas remotas**, **RSAT** en el puesto de trabajo, **Windows Admin Center** en el navegador y **PowerShell Remoting** (WinRM). Esta lección alinea vocabulario y procedimientos con lo que verás en entornos reales.

@section: Objetivos didácticos

*   Instalar **RSAT** en Windows 10/11 cliente y conectar a un dominio.
*   Conectar **Windows Admin Center** a un servidor gestionado.
*   Comprender los requisitos de **WinRM** y firewall.

@section: RSAT (Remote Server Administration Tools)

RSAT son las consolas MMC y herramientas de administración (DNS, DHCP, GPO, AD, etc.) instaladas en un **cliente Windows Pro/Enterprise**, no en el servidor.

**Instalación (Windows 10/11):**

*   **Configuración → Aplicaciones → Características opcionales → Agregar característica** → busca “RSAT” y activa los paquetes que necesites (por ejemplo: **Herramientas de administración de AD DS**, **DNS**, **DHCP**, **GPO**).

En Windows 10/11 moderno, las herramientas suelen ser **Features on Demand** descargables desde Windows Update.

**Requisito:** tu usuario debe tener permisos de administración en el servidor o delegación en OU (según tarea).

@section: Windows Admin Center (WAC)

WAC es una **consola web** ligera para gestionar servidores Windows y clústeres.

1.  Instala WAC en una máquina de administración (o en Windows 10).
2.  Abre el navegador en `https://localhost:puerto` (según instalación).
3.  **Add** → introduce FQDN o IP del servidor → credenciales.

**Ventajas:** actualizaciones de extensiones, integración con Hyper-V, certificados, eventos, servicios, sin depender de la GUI completa del servidor.

@section: WinRM y PowerShell remoto

**WinRM** (`wsman`) es el servicio que permite `Enter-PSSession` e `Invoke-Command` hacia servidores.

En el servidor (verificación rápida):

```powershell
Get-Service WinRM
Enable-PSRemoting -Force   # solo en laboratorio; revisa políticas de seguridad
```

En redes corporativas, WinRM se habilita por **GPO** o por scripts de aprovisionamiento, no manualmente servidor a servidor.

@section: Escritorio remoto (RDP)

RDP sigue usándose para:

*   Aplicaciones de administración que necesitan sesión gráfica.
*   Troubleshooting cuando WinRM falla.

**Buenas prácticas:** restricción por grupo de seguridad, **NLA**, **no** exponer RDP a Internet sin VPN o gateway.

@section: Informe de práctica (plantilla)

1.  Captura de RSAT abriendo `gpmc.msc` contra un DC de laboratorio.
2.  Captura de pantalla de Windows Admin Center mostrando una VM o servicio.
3.  Párrafo: ¿Qué canal usarías primero para **apagar un rol** (GUI, WAC, PowerShell) y por qué.

@quiz: ¿Qué conjunto de herramientas instala en Windows 10/11 las consolas MMC para administrar AD, DNS, DNS, etc. desde el cliente?
@option: Visual Studio
@correct: RSAT
@option: Office 365

@quiz: ¿Qué servicio HTTP/SOAP usa PowerShell Remoting para sesiones remotas en Windows?
@option: SSH
@correct: WinRM
@option: Telnet
