@title: Terraform Cloud / Enterprise: equipos y políticas
@icon: ☁️
@description: Workspaces remotos, runs, variables sensibles, policy-as-code con Sentinel o OPA.
@order: 5

# Terraform Cloud y el modelo de trabajo en equipo

**Terraform Cloud (TFC)** y **Terraform Enterprise (TFE)** ofrecen ejecución remota de Terraform, almacenamiento de estado gestionado, permisos por workspace, variables sensibles, historial de **runs** e integración con **policy as code**. Los nombres exactos en la interfaz evolucionan; aquí importa el **modelo mental**: código en Git, planes automáticos, aplicación controlada y políticas antes de tocar cuentas sensibles.

@section: Workspace remoto frente a ejecución local

En local ejecutas `terraform plan` y `apply` en tu máquina con credenciales locales. En TFC/TFE los **runs** se ejecutan en agentes gestionados o en **Terraform Agents** auto-hospedados cuando la política de la empresa lo exige. El código llega desde GitHub, GitLab, Bitbucket o mediante CLI/API.

Ventajas habituales:

* Estado y variables sensibles centralizados con control de acceso por equipo.
* Historial de planes y aplicaciones auditables.
* Posibilidad de **aprobación manual** antes del `apply` en producción.
* Integración con políticas (Sentinel, OPA u otras) que bloquean cambios inseguros.

@section: Flujo con control de versiones

Un flujo típico:

1. Rama de feature con cambios `.tf`.
2. Pull request que dispara un **speculative plan** (plan de solo lectura sobre el workspace vinculado) para comentar el diff en el PR.
3. Tras revisión, merge a la rama principal configurada para el workspace.
4. Nuevo run en el workspace; si las políticas pasan y las reglas de aprobación se cumplen, se permite `apply` (automático o con clic explícito según configuración).

**Run triggers** entre workspaces encadenan dependencias (por ejemplo red → compute). Úsalos con cuidado: cadenas mal diseñadas generan applies en orden incorrecto o bucles de disparos.

@section: Variables y secretos

En la UI o API de TFC defines variables por workspace:

* **Terraform variables** (prefijo `TF_VAR_` implícito en la plataforma) o **entorno** para providers.
* Marca **sensitive** lo que no debe mostrarse en logs de UI.

Buenas prácticas:

* Rotar credenciales si hubo exposición o rotación periódica por política.
* Preferir **OIDC** hacia AWS/Azure/GCP en lugar de claves de larga duración cuando el proveedor y TFC lo soporten.
* Separar workspaces por entorno (dev/stage/prod) con permisos distintos.

@section: Policy as code

**Sentinel** (ecosistema HashiCorp) u otras integraciones permiten **denegar** planes que:

* Crean instancias sin etiquetas obligatorias (`Environment`, `CostCenter`).
* Abren `0.0.0.0/22` en grupos de seguridad críticos.
* Usan tipos de instancia no permitidos o regiones no aprobadas.

Las políticas son **guardrails**: no reemplazan revisión humana, pruebas de carga ni escaneo de vulnerabilidades en imágenes; reducen errores clásicos por PR.

@section: Comparación rápida con «solo S3 + CI»

| Aspecto | TFC/TFE | Backend S3 + GitHub Actions |
|--------|---------|-----------------------------|
| Estado | Gestionado por HashiCorp | Tu bucket y políticas IAM |
| Ejecución | Agentes gestionados o propios | Runners que tú mantienes |
| Políticas | Integración nativa Sentinel/OPA | Instalar herramientas en CI |
| Coste | Licencia/suscripción | Infra CI + tiempo de equipo |

Muchos equipos empiezan con S3 + CI y migran a TFE cuando necesitan **gobierno** centralizado y aprobaciones fuertes.

@section: Cuándo considerar on-prem o no usar TFC

* Requisitos regulatorios de datos que no pueden salir de tu red.
* Política de la empresa de no usar SaaS para credenciales.
* Coste: evalúa si el valor aportado supera el de un pipeline bien diseñado en casa.

@section: Errores operativos

* Workspace apuntando a la rama equivocada y applies silenciosos sobre el entorno incorrecto.
* Variables sensibles duplicadas entre workspace y repositorio (fuente de verdad confusa).
* Políticas tan estrictas que bloquean trabajo legítimo sin proceso de excepción documentado.

@section: Laboratorio sugerido

1. Crea una cuenta de prueba en Terraform Cloud (tier gratuito si está disponible).
2. Conecta un repositorio Git con un proyecto mínimo de un proveedor de nube de prueba.
3. Configura un workspace, ejecuta un run de plan y revisa el informe.
4. (Opcional) Añade una política de ejemplo que exija una etiqueta `Environment` en recursos de compute y observa el fallo controlado cuando falta.

@quiz: ¿Qué ventaja principal ofrece Terraform Cloud frente a ejecutar Terraform solo en un portátil?
@option: Hace el código HCL innecesario
@correct: Ejecución centralizada, estado gestionado, historial de runs y controles de acceso/equipo
@option: Elimina la necesidad de proveedores cloud
