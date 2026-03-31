@title: Server Manager y Consolas MMC
@icon: 📊
@description: Las herramientas gráficas para la administración diaria.
@order: 3

# Server Manager y consolas MMC

**Server Manager** es el panel central de roles y características en Windows Server. Las **MMC** (consolas de administración) siguen siendo el estándar para DNS, DHCP, usuarios, etc. Dominarlas es requisito en mesa de ayuda y soporte de segundo nivel.

@section: Objetivos didácticos

*   Instalar roles y características desde Server Manager y desde PowerShell.
*   Abrir y **anclar** consolas MMC frecuentes.
*   Entender el **dashboard** y la gestión de grupos de servidores.

@section: Server Manager al arrancar

Al iniciar sesión, Server Manager muestra:

*   **Bienvenida Local Server:** nombre, dominio, red, actualizaciones, Remote Desktop.
*   **Todos los servidores:** lista de equipos gestionados (puedes añadir servidores remotos).
*   **Roles y características:** acceso rápido a instalación/desinstalación.

**Buena práctica:** configura primero **Actualizaciones** y **Red** antes de instalar roles críticos.

@section: Instalar roles y características (GUI)

1.  **Administrar → Agregar roles y características**.
2.  Tipo de instalación: **basada en roles** o **basada en características**.
3.  Selecciona el servidor (local o remoto).
4.  Marca el rol (ej. **Active Directory Domain Services**); el asistente sugerirá **herramientas de administración** (RSAT).
5.  Completa el asistente y **reinicia** si el rol lo requiere.

@section: Equivalente en PowerShell (referencia)

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

Documenta en tus prácticas el comando equivalente a cada clic: es lo que pedirán en entrevistas técnicas.

@section: Consolas MMC (snap-ins)

Ejemplos habituales:

| Consola | Uso |
| :--- | :--- |
| `dnsmgmt.msc` | Zonas DNS, registros A/AAAA/CNAME |
| `dhcpmgmt.msc` | Ámbitos, reservas, opciones |
| `dsa.msc` | Usuarios y equipos de Active Directory |
| `gpmc.msc` | Objetos de directiva de grupo |

**Tip:** `Win + R` → escribe el `.msc` → Enter. Puedes guardar una consola personalizada con **mmc.exe** y **File → Add/Remove Snap-in**.

@section: Administración remota

Server Manager permite **Add other servers to manage** (mismo dominio, credenciales). Requiere:

*   Firewall y **WinRM** configurados.
*   Permisos de administración en el servidor remoto.

En entornos reales, muchos equipos migran a **Windows Admin Center** (navegador), pero las MMC siguen vivas en operaciones diarias.

@section: Práctica de aula

1.  Instala el rol **DNS** en una VM de laboratorio.
2.  Abre `dnsmgmt.msc` y crea una **zona de prueba** `lab.local`.
3.  Anota en tu cuaderno: ¿qué rol necesita el servidor DC para que **DNS integrado en AD** funcione?

@quiz: ¿Qué extensión tienen las consolas de administración de Microsoft Management Console?
@option: .exe
@correct: .msc
@option: .dll

@quiz: ¿Qué ventaja tiene instalar roles con `-IncludeManagementTools` en PowerShell?
@option: Hace el servidor más rápido
@correct: Instala también las herramientas de administración (RSAT) necesarias para gestionar el rol
@option: Elimina la necesidad de reiniciar
