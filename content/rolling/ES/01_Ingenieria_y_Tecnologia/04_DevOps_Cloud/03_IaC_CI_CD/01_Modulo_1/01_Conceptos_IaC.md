@title: Conceptos de IaC y Proveedores Cloud
@icon: 📜
@description: Declarativo vs imperativo, idempotencia, estado y modelo mental de la infraestructura como código.
@order: 1

# Infraestructura como código: el mapa mental correcto

Antes de escribir un solo `.tf` o un playbook, necesitas **vocabulario común** con tu equipo y con las herramientas. Esta lección fija definiciones que, mal entendidas, generan incidentes: «apliqué el cambio pero volvió atrás», «el estado no cuadra», «en mi máquina funciona». El objetivo es que puedas explicar **por qué** versionas la infra y **cómo** se relaciona eso con Git, pipelines y auditoría.

@section: Qué es IaC (y qué no es)

**Infraestructura como código (IaC)** es la práctica de describir recursos (redes, máquinas, balanceadores, bases de datos gestionadas, permisos, DNS, certificados) en **archivos versionados** y aplicar cambios mediante **procesos reproducibles** (CLI, pipelines, políticas de revisión y, en muchos equipos, aprobaciones explícitas).

Lo que **no** es IaC:

* Un script bash que solo ejecuta comandos en orden sin modelo de estado (eso es automatización **imperativa** frágil si falla a mitad o si se re-ejecuta sin cuidado).
* Copiar y pegar en la consola web del cloud sin dejar rastro en Git (no hay trazabilidad ni revisión por pares).
* Un job de CI que llama APIs sin declarar el **estado deseado** explícito ni documentar el impacto.

La diferencia clave: IaC asume que **el sistema tiene un modelo** (aunque sea implícito en el motor) de «cómo debe quedar el mundo» frente a «cómo está ahora». Ese modelo puede vivir en archivos HCL, YAML de CloudFormation, manifiestos de Kubernetes o playbooks idempotentes.

@section: Imperativo vs declarativo

En el enfoque **imperativo** dices *los pasos*: «crea VPC, crea subred, abre el puerto 443». Si falla a mitad, el sistema queda a medias y el siguiente intento puede chocar con recursos parcialmente creados.

En el enfoque **declarativo** dices *el resultado*: «quiero una VPC con estas subredes, estas tablas de rutas y estas reglas de firewall». El motor (Terraform, CloudFormation, Pulumi con modelo declarativo, etc.) calcula un **plan de diferencias** entre el estado conocido y el deseado.

**Terraform** es principalmente declarativo: describes recursos y dependencias; el motor propone `plan` y luego `apply`.  
**Ansible** suele ser **imperativo** en la ejecución (lista ordenada de tareas), pero los módulos bien diseñados son **idempotentes** y convergen hacia un estado deseado sin acumular efectos colaterales.

En la práctica muchos equipos combinan: Terraform (u otro IaC de nube) para **capa de proveedor**, Ansible o imágenes inmutables para **configuración dentro del SO**, y Kubernetes para **carga de trabajo**. La frontera no es dogmática; lo importante es saber **quién es la fuente de verdad** para cada capa.

@section: Idempotencia

Una operación es **idempotente** si ejecutarla varias veces deja el mismo resultado que ejecutarla una vez (salvo efectos externos como datos generados por usuarios).

Ejemplo malo: **siempre** `echo "foo" >> /etc/app.conf` — no es idempotente (el archivo crece).

Ejemplo bueno: módulo `lineinfile` o `copy` con contenido fijo que garantiza **exactamente** la línea deseada.

**Por qué importa:** los pipelines se reintentan; los playbooks se repiten tras fallos; los nodos se reinstalan. Si tu automatización no es idempotente, cada re-ejecución puede **acumular basura**, duplicar reglas o corromper configuración.

@section: Estado (state) y deriva

La mayoría de herramientas declarativas necesitan un **estado**: mapa de identificadores de recursos en la nube, metadatos de dependencias y a veces valores sensibles. Ese estado puede vivir:

* En **archivo local** (`terraform.tfstate`) — válido para pruebas personales, frágil en equipo.
* En **backend remoto** (S3 con bloqueo, Terraform Cloud, Azure Storage, etc.) con cifrado y permisos finos.

**Deriva (drift):** alguien cambió la consola web, un script externo o una API y el estado ya no refleja la realidad. El siguiente `plan` puede proponer **recrear** recursos o **ajustar** atributos de forma inesperada.

Mitigación habitual:

* Política de equipo: cambios estructurales **solo** vía código y pipeline.
* IAM que limite quién puede editar recursos críticos en consola.
* Escaneos periódicos o `terraform plan` en modo lectura para detectar drift.

@section: Proveedores y recursos (Terraform)

En Terraform, un **provider** es un plugin que traduce bloques HCL (`aws_instance`, `azurerm_linux_virtual_machine`) en llamadas API.

Cada **recurso** tiene tipo, nombre local y argumentos. Los **data sources** leen objetos existentes sin crearlos (AMIs, VPC por filtro, zonas DNS).

**Versión de provider:** fija versiones en `required_providers` para evitar que un `terraform init` descargue un major que cambia esquemas y rompe pipelines de un día para otro.

@section: GitOps (visión operativa)

**GitOps** usa Git como **fuente de verdad** operativa: merges aceptados disparan reconciliación (Argo CD, Flux) o aplicación de Terraform en CI. Beneficios: PR con diff visible, revert conocido, auditoría por commit.

No es obligatorio para «hacer IaC», pero es el estándar en equipos que quieren **cambios revisados** antes de tocar producción.

@section: Tabla rápida: herramienta vs rol

| Herramienta / enfoque | Fortaleza típica | Cuidado |
|----------------------|------------------|--------|
| Terraform | APIs de cloud, módulos, estado | Drift si se mezcla consola |
| CloudFormation / Bicep | Nativo AWS/Azure, stacks | Vendor lock-in del modelo |
| Ansible | SO, heterogeneidad, sin agente | Playbooks largos sin roles |
| Pulumi | Código real (TS, Go…) | Curva para quien solo quiere HCL |

@section: Laboratorio mental

1. Enumera tres recursos de tu cloud que quieras versionar (VPC, SG, RDS, bucket).
2. Para cada uno: ¿hay API estable? ¿quién puede cambiarlo fuera de Git?
3. Escribe una regla de equipo en una frase: por ejemplo «cambios de SG en producción solo con PR y ventana de cambio».

@section: Errores frecuentes

* Tratar IaC como «script de una vez» sin `plan` ni revisión.
* Compartir `tfstate` por email o sin bloqueo (riesgo de corrupción y conflictos).
* Mezclar entornos en el mismo state sin workspaces o stacks separados.
* Asumir que «declarativo» significa «no hay que entender el orden»: las dependencias implícitas y explícitas siguen importando.

@section: Cierre

IaC no es magia: es **disciplina** de modelar el mundo, versionar el modelo y aplicarlo con herramientas que entiendan estado y APIs. Lo siguiente en este track es bajar a **HCL y Terraform** con la mano en el código.

@quiz: ¿Qué característica distingue una automatización declarativa típica de Terraform frente a un script bash imperativo?
@option: El script bash siempre es más rápido
@correct: La declarativa describe el estado deseado y el motor calcula diferencias; el imperativo lista pasos secuenciales
@option: No hay diferencia práctica

@quiz: ¿Qué es la “deriva” (drift) en IaC?
@option: Usar Git en lugar de SVN
@correct: Diferencia entre lo declarado en código/estado y lo realmente desplegado en el proveedor
@option: Borrar el estado por accidente
