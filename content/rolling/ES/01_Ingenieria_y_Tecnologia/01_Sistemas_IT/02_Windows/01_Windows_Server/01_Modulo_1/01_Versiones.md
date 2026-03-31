@title: Versiones de Windows Server y Licenciamiento
@icon: 💳
@description: Entendiendo las ediciones Standard, Datacenter y Essentials.
@order: 1

# Versiones de Windows Server y licenciamiento (nivel instituto)

Esta unidad forma parte de un perfil de **administrador de sistemas Windows** en entornos reales de PYME y corporativos. Saber elegir edición y modelo de licencia evita costes innecesarios y problemas de cumplimiento ante auditorías.

@section: Objetivos didácticos

Al finalizar esta lección podrás:

*   Diferenciar **Standard**, **Datacenter** y **Essentials** según escenario (virtualización, núcleos, nube híbrida).
*   Explicar el licenciamiento por **núcleo físico** (Core + CAL) en el lenguaje que usa un proveedor o un departamento de compras.
*   Relacionar cada edición con **roles** típicos: AD DS, Hyper-V, almacenamiento, clustering.

@section: Ciclo de vida y canales de actualización

Windows Server se publica en ciclos **LTSC** (Long-Term Servicing Channel): versiones estables pensadas para servidores que deben permanecer años en producción. Consulta siempre la documentación oficial de Microsoft para la versión concreta (por ejemplo **Windows Server 2022**) respecto a fechas de soporte **mainstream** y **extended**.

*   **Actualizaciones acumulativas:** parches mensuales de seguridad y calidad; se planifican en ventanas de mantenimiento.
*   **Canal de instalación:** imagen ISO desde Microsoft Evaluation Center o licencias por volumen (VL); en aulas suele usarse evaluación de 180 días prorrogable para laboratorio.

@section: Ediciones principales

### Standard

*   Incluye la mayoría de roles (AD DS, DNS, DHCP, archivos, IIS, etc.).
*   Adecuada cuando el número de **máquinas virtuales protegidas por licencia** es limitado según la política de licenciamiento vigente (Microsoft detalla límites por versión; verifica en la guía oficial de la versión que instales).
*   Escenario típico: varios servidores físicos con poca densidad de VMs o entornos de **infraestructura tradicional**.

### Datacenter

*   Orientada a **alta densidad de virtualización** y características avanzadas de centro de datos (según versión: por ejemplo, **Storage Replica**, **Shielded VMs**, etc. — revisa la matriz de características de tu versión).
*   Suele elegirse cuando el coste total por núcleo y VM resulta más favorable que acumular licencias Standard en un clúster de virtualización.

### Essentials (donde siga disponible)

*   Pensa en **muy pequeña empresa**: límites de usuarios/dispositivos y roles integrados; no es un reemplazo de un dominio Enterprise completo. Verifica si Microsoft sigue ofreciendo la edición para tu versión objetivo.

@section: Licenciamiento por núcleo y CAL

Desde Windows Server 2016, el modelo habitual es:

1.  **Licencias base por servidor** cubriendo **todos los núcleos físicos** del host (mínimos por CPU según tabla de Microsoft).
2.  **CALs (Client Access Licenses):** licencias de acceso para **usuarios** o **dispositivos** que usan los servicios del servidor (autenticación, impresión, archivos, etc.). No es lo mismo “tener el servidor instalado” que “tener derecho a que 200 usuarios lo usen”.

En un informe técnico para jefatura deberías poder resumir: *“Necesitamos X licencias de núcleo para el host y Y CALs de usuario según el censo de puestos.”*

@section: Laboratorio mental (sin máquina)

1.  Enumera tres roles que vas a desplegar en tu proyecto (ej. AD, DNS, archivos).
2.  Decide si el servidor será **bare metal** o **VM** y si habrá **más de un host** de virtualización.
3.  Justifica en dos frases si orientarías la compra hacia **Standard** o **Datacenter** y por qué.

@section: Errores comunes en prácticas de instituto

*   Instalar **Datacenter** “porque suena mejor” en un único host con dos VMs: puede ser económicamente incorrecto.
*   Olvidar las **CAL** en el presupuesto: la auditoría de licencias puede ser tan crítica como la seguridad técnica.
*   Mezclar versiones de esquema de AD sin plan: afecta a la **compatibilidad** entre controladores de dominio.

@quiz: ¿Qué componente de licencia cubre el derecho de un usuario a conectarse a servicios de un servidor Windows (archivos, autenticación, etc.)?
@option: Licencia OEM del PC
@option: Solo la licencia del sistema operativo del servidor
@correct: CAL (usuario o dispositivo)
@option: Licencia de antivirus

@quiz: ¿Qué edición suele elegirse para alta densidad de virtualización en un clúster de hipervisores Windows Server, según la práctica comercial habitual?
@option: Essentials
@correct: Datacenter
@option: Home Basic

@quiz: ¿Qué debes verificar siempre antes de planificar una subida de versión de Windows Server en un dominio existente?
@option: Solo el espacio en disco
@correct: Matriz de compatibilidad de roles, esquema de AD y soporte de aplicaciones
@option: El color del tema del escritorio
