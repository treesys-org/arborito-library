@title: Gestión de Estado Remoto y Bloqueo
@icon: 🔐
@description: Backends S3, bloqueo, workspaces y buenas prácticas para equipos.
@order: 3

# Estado remoto, bloqueo y equipos

El archivo `terraform.tfstate` es el **índice** de lo que Terraform cree haber creado: IDs reales en el proveedor, dependencias y a veces datos derivados. Sin estado coherente, los planes son impredecibles o destructivos. En equipos, el estado **solo local** no escala: necesitas **backend remoto**, **cifrado**, **bloqueo** y **permisos** para que dos personas o pipelines no corrompan el mismo despliegue.

@section: Por qué Git no sustituye al backend remoto (como única solución)

Git versiona **código** de forma excelente. El estado, sin embargo:

* Cambia en cada `apply` con IDs y atributos que no quieres fusionar como si fueran código de aplicación.
* Puede contener valores sensibles si alguien los metió en recursos sin cuidado.
* Debe ser la **vista actual** que Terraform usa para el siguiente plan, no un historial de merges.

La práctica habitual es **backend remoto** con cifrado en reposo, control de acceso por rol y a veces replicación; los commits del `.tfstate` al repo de aplicación son excepción, no regla.

@section: Backend S3 + DynamoDB (patrón habitual en AWS)

Configuración típica:

```hcl
terraform {
  backend "s3" {
    bucket         = "mi-empresa-tfstate"
    key            = "proyecto/vpc/terraform.tfstate"
    region         = "eu-west-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

* **S3** almacena el objeto de estado; activa versionado de objetos para poder restaurar si algo sale mal.
* **DynamoDB** proporciona **bloqueo**: si un `apply` está en curso, otro proceso obtiene error de lock hasta que termine o hasta liberación explícita.

El bucket y la tabla suelen crearse en un **bootstrap** separado o en una cuenta de organización, con políticas que impiden borrado accidental.

@section: Alternativas de backend

* **Terraform Cloud / Enterprise**: estado gestionado, runs, permisos por workspace.
* **Azure Storage** con blob y lease, **GCS** con buckets versionados.
* **Consul**, **etcd** en entornos muy específicos.

Elige según cumplimiento (datos en región X), coste y madurez del equipo en esa plataforma.

@section: Workspaces de Terraform

`terraform workspace` permite **varios estados** bajo el mismo código con prefijos en el backend (`env:/dev/`, `env:/prod/`). Ventaja: un solo directorio. Riesgo: confundir el workspace activo y aplicar en el entorno equivocado.

Alternativa muy usada: **directorios por entorno** (`envs/dev`, `envs/prod`) con backends distintos y variables explícitas; más verboso pero más difícil de equivocarse si el proceso de CI es claro.

@section: Migración de estado local a remoto

Procedimiento seguro:

1. Acuerda una ventana donde nadie más ejecuta `apply` sobre ese stack.
2. Crea el backend remoto y permisos IAM/rol para CI y humanos autorizados.
3. Añade el bloque `backend` y ejecuta `terraform init -migrate-state`; Terraform pedirá confirmación.
4. Verifica en el almacenamiento remoto que el objeto existe.
5. Ejecuta `terraform plan` y confirma que **no** propone recreaciones masivas inesperadas (si las hay, revisa nombres de recursos y providers antes de continuar).

@section: Rotación, backup y recuperación

* Habilita **versionado** de objetos de estado en S3 o equivalente.
* Antes de cambios grandes (refactor de módulos, imports masivos), exporta una copia o confía en versiones previas del objeto.
* **Estado corrupto o bloqueado:** la documentación oficial describe `terraform state` subcomandos y `import`; `force-unlock` solo cuando confirmas que no hay apply real en curso.

@section: Buenas prácticas de seguridad y operación

* IAM mínimo: el rol de CI solo necesita permisos sobre el bucket de estado y sobre los recursos del proyecto (idealmente en cuenta dedicada).
* Separa **cuentas** cloud por entorno cuando la organización lo permita; reduce blast radius.
* Etiqueta recursos con `Environment`, `Owner`, `TerraformWorkspace` o equivalente para auditoría y costes.

@section: Situaciones incómodas (y qué hacer)

* **Dos equipos tocando el mismo stack:** unifica responsabilidad o divide el código en stacks con `terraform_remote_state` y contratos claros de outputs.
* **Drift detectado en plan:** decide si importas la realidad al código, reviertes en consola o aceptas el cambio propuesto por Terraform; nunca a ciegas en producción.

@section: Laboratorio sugerido

1. Crea un bucket S3 privado con cifrado y versionado, y una tabla DynamoDB para locks (sigue la guía actual de Terraform para el backend `s3`).
2. Configura un proyecto mínimo con backend remoto y ejecuta `init`, `plan` y un `apply` de prueba en cuenta sandbox.
3. Lanza dos `apply` concurrentes desde dos terminales y observa el comportamiento de bloqueo; libera el lock correctamente al terminar.

@quiz: ¿Para qué sirve la tabla DynamoDB en el backend S3 clásico de Terraform?
@option: Almacenar el código HCL
@correct: Proporcionar bloqueo (locking) para evitar applies concurrentes sobre el mismo estado
@option: Guardar logs de auditoría obligatorios

@quiz: ¿Qué riesgo principal hay al guardar terraform.tfstate en un repositorio Git público?
@option: Que Terraform vaya más lento
@correct: Exposición de IDs de recursos y posible fuga de datos sensibles del despliegue
@option: Que Git rechace archivos .tf
