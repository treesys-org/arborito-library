@title: Servidores Pull vs Push
@icon: 🎯
@description: Dos modelos para aplicar y mantener la configuración deseada.
@order: 3

# DSC Pull y Push

**Push:** el administrador envía la configuración al nodo (`Start-DscConfiguration`). **Pull:** el nodo obtiene su MOF y módulos desde un **servidor SMB/HTTP** o **Azure Automation**.

@section: Push

*   Simple para pocos servidores.
*   Requiere conectividad entrante o WinRM desde estación de administración.

@section: Pull Server (SMB/HTTP)

*   El nodo LCM se registra con un **ConfigurationID** o **RegistrationKey** (según versión).
*   Descarga configuración y **checksums** de recursos.

**Mantenimiento:** los pull servers on-premises han sido **desaconsejados** para nuevos diseños en favor de soluciones cloud; verifica guías actuales de Microsoft.

@section: Azure / guest policy

En muchas organizaciones, **Azure Automation** o **Guest Configuration** sustituyen el pull DSC clásico.

@section: Seguridad

Cifra tráfico (HTTPS), protege claves de registro, **firma** configuraciones.

@section: Práctica (conceptual)

1.  Dibuja en papel un escenario push: tu PC → 5 servidores.
2.  Dibuja pull: 200 servidores → servidor pull → reportes de cumplimiento.

@quiz: ¿Qué modelo de DSC envía la configuración desde el administrador hacia el nodo cuando se ejecuta Start-DscConfiguration?
@option: Pull
@correct: Push
@option: Hybrid Sync

@quiz: ¿Qué ventaja tiene Pull en grandes despliegues?
@option: No requiere red
@correct: Los nodos pueden obtener su configuración de forma escalable sin sesiones administrativas simultáneas a cada servidor
@option: Elimina la necesidad de HTTPS
