@title: Terraform: HCL, Recursos y Variables
@icon: 🧱
@description: Sintaxis HCL, bloques resource/data, variables, outputs y flujo terraform init/plan/apply.
@order: 2

# Terraform en la práctica: HCL, recursos y variables

Terraform es un motor **declarativo** que lee configuración en **HCL** (HashiCorp Configuration Language), calcula un **plan** de cambios respecto al **estado** y aplica esos cambios vía APIs de proveedores. Esta lección cubre lo necesario para leer stacks existentes, escribir recursos nuevos y no perderte cuando el `plan` muestra cientos de líneas en un entorno real.

@section: Bloques básicos

Un archivo `.tf` combina bloques con significados distintos:

* **`terraform`**: configuración del propio Terraform (`required_version`, `backend`, `required_providers`, `experiments` ocasionales).
* **`provider`**: credenciales, región por defecto y features (a menudo vía variables de entorno como `AWS_PROFILE`).
* **`resource`**: crea o actualiza un objeto en el proveedor.
* **`data`**: lee objetos existentes (solo lectura).
* **`variable`**: entrada parametrizable con tipo y validación.
* **`output`**: expone valores tras el apply (IPs, DNS, ARNs) para humanos o para otros sistemas.
* **`locals`**: valores derivados para no repetir expresiones largas.

Ejemplo mínimo ilustrativo (nombres de recursos genéricos):

```hcl
terraform {
  required_version = ">= 1.5.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

variable "aws_region" {
  type    = string
  default = "eu-west-1"
}

variable "project" {
  type = string
}

resource "aws_s3_bucket" "logs" {
  bucket = "${var.project}-logs"
}

output "bucket_name" {
  value = aws_s3_bucket.logs.id
}
```

@section: Referencias y meta-argumentos

Dentro de un recurso puedes referenciar otros:

* `aws_instance.web.id` — atributos exportados por el recurso tras creación.
* `data.aws_ami.alpine.id` — datos de una AMI existente filtrada por nombre.

Meta-argumentos que debes conocer:

* **`depends_on`**: dependencia explícita cuando Terraform no infiere orden (por efectos laterales o APIs raras).
* **`count`**: crea N copias indexadas (`resource.x[0]`).
* **`for_each`**: mapa o set de instancias con claves estables (preferible a `count` cuando el orden importa poco pero los identificadores sí).
* **`lifecycle`**:  
  - `prevent_destroy` evita borrados accidentales en recursos críticos.  
  - `ignore_changes` congela atributos que el proveedor modifica fuera de Terraform.  
  - `create_before_destroy` ayuda en recursos que soportan reemplazo sin ventana larga de caída.

@section: Flujo de trabajo CLI

1. **`terraform init`**: descarga providers, configura backend de estado, prepara plugins.
2. **`terraform fmt`**: formatea archivos (intégralo en pre-commit).
3. **`terraform validate`**: valida sintaxis y tipos sin llamar al cloud (útil en CI rápido).
4. **`terraform plan`**: calcula y muestra el diff propuesto; **revísalo** antes de aplicar.
5. **`terraform apply`**: aplica cambios; en CI a menudo se guarda el plan binario y se aplica solo ese artefacto.

Patrón seguro en CI: `terraform plan -out=plan.tfplan` en un job; job posterior ejecuta `terraform apply plan.tfplan` tras aprobación o merge.

@section: Variables: tipos y precedencia

Orden de precedencia típico (de menor a mayor prioridad):

1. Valores por defecto en bloques `variable`.
2. Archivo `terraform.tfvars` / `*.auto.tfvars`.
3. `-var` y `-var-file` en línea de comandos.
4. Variables de entorno `TF_VAR_nombre`.

Tipos: `string`, `number`, `bool`, colecciones (`list`, `map`, `set`), y compuestos (`object`, `tuple`). Usa bloques `validation` en `variable` para rechazar valores absurdos antes del `plan`.

@section: Outputs y datos sensibles

`output` puede marcar `sensitive = true` para reducir exposición en logs de consola. **No** es cifrado fuerte: evita volcar secretos a pipelines públicos o a notificaciones de chat.

Cuando un output alimenta otro sistema (por ejemplo un pipeline que despliega Helm), documenta el contrato: tipo, formato y si puede ser nulo en entornos de prueba.

@section: Errores típicos en el día a día

* Olvidar `terraform init` tras clonar o añadir un provider nuevo.
* Subir `.tfstate` a repositorios públicos (filtra IDs y a veces datos internos).
* Usar `apply -auto-approve` en producción sin revisar el `plan` o sin artefacto de plan fijado.
* No fijar versiones de provider: el mismo código produce planes distintos semanas después.
* Mezclar mayúsculas y convenciones de nombres de recursos que el proveedor normaliza (sorpresas en imports).

@section: Cuándo usar `data` frente a `resource`

* **`resource`**: Terraform es responsable del ciclo de vida del objeto.
* **`data`**: solo consultas; el objeto puede haber sido creado por otro equipo o proceso.

Si necesitas «adoptar» un recurso existente, el flujo oficial suele ser `terraform import` (cuidadoso: requiere alinear configuración con la realidad).

@section: Laboratorio sugerido

1. Instala Terraform en tu entorno local o usa un contenedor oficial.
2. Crea un directorio con `main.tf` que defina un recurso mínimo en una cuenta de prueba (por ejemplo un bucket con nombre único) parametrizado por `variable "project"`.
3. Ejecuta `init`, `validate`, `plan` **sin** `apply` primero y lee cada sección del plan.
4. Añade un `output` y repite el plan; observa cómo cambia la salida.

@quiz: ¿Qué comando debes ejecutar tras clonar un repositorio Terraform antes de `plan`?
@option: terraform apply
@correct: terraform init
@option: terraform refresh solamente

@quiz: ¿Para qué sirve principalmente `terraform plan`?
@option: Formatear archivos .tf
@correct: Mostrar qué cambios aplicaría Terraform sin ejecutarlos hasta que confirmes apply
@option: Borrar el estado
