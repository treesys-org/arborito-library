@title: Controladores de Dominio y Roles FSMO
@icon: 👑
@description: Los servidores que autentican y replican la información del directorio.
@order: 2

# Controladores de dominio y roles FSMO

Un **controlador de dominio (DC)** es un servidor Windows Server que ejecuta **AD DS** y replica **LDAP/Kerberos**. Los **roles FSMO** son operaciones que **no pueden** ejecutarse en paralelo en todo el bosque o dominio y se asignan a DC concretos.

@section: Objetivos didácticos

*   Promover un servidor a DC y **verificar** salud básica.
*   Nombrar los **cinco roles FSMO** y su función.
*   Planificar **al menos dos DC** en producción.

@section: Promoción a DC

Tras instalar el rol **AD DS**, el asistente **promueve** el servidor:

1.  **Nuevo bosque** (primer DC) o **añadir a dominio existente**.
2.  Define **nombre NetBIOS** y **nivel funcional** del bosque/dominio (según compatibilidad).
3.  DNS integrado en AD (recomendado).

**Comprobación:** `dcdiag /v` en laboratorio; en producción, ejecutar con cambios de ventana.

@section: Roles FSMO (5)

| Rol | Ámbito | Función resumida |
| :--- | :--- | :--- |
| **Schema Master** | Bosque | Actualiza el esquema de AD |
| **Domain Naming Master** | Bosque | Añade/quita dominios en el bosque |
| **PDC Emulator** | Dominio | Tiempo, bloqueos de contraseña, compatibilidad |
| **RID Master** | Dominio | Asigna pools de RID para nuevos objetos |
| **Infrastructure Master** | Dominio | Referencias entre dominios (según topología GC) |

Para ver roles:

```powershell
netdom query fsmo
```

@section: Transferencia vs captura

*   **Transferencia:** coordinada entre DCs; método **correcto** en mantenimiento.
*   **Captura (seize):** forzada cuando un DC **muere**; riesgo de inconsistencias; solo en emergencia.

@section: Mínimo dos DC

En producción, **un solo DC** implica **punto único de fallo**: autenticación, DNS, GPO.

*   **Segundo DC** en el mismo sitio o remoto según **SLA**.
*   **Backup** de **System State** de AD (no basta copiar carpetas).

@section: Read-only DC (RODC)

En sucursales de baja seguridad física, un **RODC** almacena credenciales **cachéables** de forma limitada y reduce riesgo de robo del directorio completo.

@section: Laboratorio

1.  Crea un segundo DC virtual en la misma red.
2.  Verifica replicación: `repadmin /replsummary`.
3.  Documenta quién tiene cada rol FSMO.

@quiz: ¿Qué rol FSMO es crítico para la sincronización de tiempo Kerberos en el dominio?
@option: Schema Master
@correct: PDC Emulator
@option: RID Master

@quiz: ¿Cuándo usarías "seize" en lugar de "transfer" para un rol FSMO?
@option: Cuando quieres actualizar Windows más rápido
@correct: Cuando el DC que poseía el rol está offline de forma definitiva o corrupto
@option: Nunca
