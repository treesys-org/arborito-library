@title: Gestión de Usuarios, Grupos y OUs
@icon: 🧑‍💼
@description: Creando y organizando identidades y sus permisos.
@order: 3

# Gestión de usuarios, grupos y OUs

La **identidad** en AD es la base del acceso a carpetas, aplicaciones y VPN. En instituto debes dominar **creación**, **desactivación**, **grupos anidados** y **delegación** sobre OU sin dar Domain Admin a todo el mundo.

@section: Objetivos didácticos

*   Crear usuarios y **plantillas** de cuenta.
*   Usar **grupos de seguridad** con criterio de alcance (local de dominio, global, universal).
*   Delegar tareas sobre OU (reset de contraseña, unión a dominio).

@section: Creación de usuarios

Herramientas: **Active Directory Users and Computers (ADUC)** o **PowerShell**:

```powershell
New-ADUser -Name "Ana Lopez" -SamAccountName "alopez" `
  -UserPrincipalName "alopez@dominio.local" -Path "OU=Ventas,DC=dominio,DC=local" `
  -AccountPassword (ConvertTo-SecureString "TempPassw0rd!" -AsPlainText -Force) -Enabled $true
```

**Buenas prácticas:**

*   Contraseña temporal + **cambio obligatorio** al primer inicio.
*   **Descripción** y **cargo** rellenados para auditoría.
*   Cuentas de servicio con **GMSA** (cuando aplique) en lugar de contraseñas estáticas en scripts.

@section: Grupos

*   **Grupos de seguridad:** para permisos en recursos.
*   **Grupos de distribución:** principalmente correo (no para ACL NTFS).

**Alcance (simplificado):**

*   **Global:** miembros del mismo dominio; se usan en multidominio con cuidado.
*   **Domain Local:** pueden contener miembros de cualquier dominio de confianza; suelen usarse en ACL de recursos.
*   **Universal:** miembros de cualquier dominio del bosque; replicación GC.

En la práctica: **un solo dominio** → grupos globales por rol (`GG-Ventas-Lectura`) y asignación a ACL.

@section: Unidades organizativas

*   **Delegación:** clic derecho en OU → **Delegate Control** → asistente.
*   **Bloqueo de herencia:** una OU puede **no heredar** GPOs del padre (útil para excepciones; documentar bien).

@section: Ciclo de vida

1.  **Alta:** usuario + pertenencia a grupos.
2.  **Cambio de rol:** mover de OU, actualizar grupos.
3.  **Baja:** **deshabilitar** antes de borrar; conservar **SID history** no aplica igual que en migraciones; en baja definitiva, revisar **mailbox** y **home folders**.

@section: Informe de práctica

Crea en laboratorio:

*   OU `OU=Prueba,DC=lab,DC=local`
*   Grupo `GG-Prueba-RW`
*   Usuario miembro del grupo
*   Carpeta compartida con permisos **solo** al grupo (no “Everyone”)

@quiz: ¿Qué acción es más segura al despedir a un empleado antes de eliminar la cuenta?
@option: Borrar la cuenta inmediatamente
@correct: Deshabilitar la cuenta, revocar sesiones y luego retirar pertenencias a grupos
@option: Dejar la cuenta activa sin contraseña

@quiz: ¿Para qué sirve delegar control sobre una OU?
@option: Para instalar juegos
@correct: Para permitir a ayudantes de IT tareas limitadas (p. ej. reset de contraseña) sin ser Domain Admin
@option: Para borrar el bosque
