@title: Módulos Terraform: composición y reutilización
@icon: 📦
@description: module {}, fuentes locales y registry, versionado y patrones de diseño.
@order: 4

# Módulos Terraform: componer sin copiar y pegar

Un **módulo** es un directorio con un conjunto de `.tf` que encapsula recursos relacionados: red, bases de datos, Kubernetes, etc. Los módulos evitan duplicar cientos de líneas, permiten **versionar interfaces** (`variables` + `outputs`) y ocultan detalle interno a quien solo consume el contrato. Esta lección cubre llamadas, fuentes, contratos y errores que ves en proyectos reales.

@section: Llamar a un módulo (root module)

```hcl
module "vpc" {
  source = "./modules/vpc"

  cidr_block = "10.0.0.0/16"
  azs        = ["eu-west-1a", "eu-west-1b"]
}

output "vpc_id" {
  value = module.vpc.vpc_id
}
```

El **nombre** del bloque (`vpc`) es local al stack. Los outputs del módulo se exponen como `module.vpc.<nombre_output>`. Los recursos internos se nombran en el estado con prefijos como `module.vpc.aws_vpc.this`.

@section: Fuentes de módulos

* **Ruta local:** `source = "./modules/network"` — rápido en monorepo; cambios y PRs conjuntos.
* **Git con ref fija:**  
  `source = "git::https://github.com/org/tf-modules.git//vpc?ref=v1.2.0"`  
  Ancla **tags** o **commits SHA**; evita `main` flotante en producción.
* **Registry público o privado:** por ejemplo `terraform-aws-modules/vpc/aws` con `version = "~> 5.0"`.

Regla práctica: si el módulo no es tuyo, **pin** de versión y lee el CHANGELOG antes de subir major.

@section: Contrato del módulo: inputs y outputs

Un buen módulo documenta:

* **Inputs:** tipos, valores por defecto, validaciones (`validation` en `variable`).
* **Outputs:** solo lo que el consumidor necesita; no exponer secretos ni IDs internos irrelevantes.
* **Supuestos:** por ejemplo «crea subnets solo en modo público» o «requiere cuenta con límites de EIP suficientes».

Antipatrón frecuente: módulo «dios» con decenas de variables obligatorias sin defaults sensatos. Mejor **componer** módulos pequeños (red, SG, RDS) que se llaman desde el root o desde un módulo intermedio.

@section: Raíz vs hijos y anidamiento

El **root module** es el directorio desde el que ejecutas `terraform apply`. Los módulos hijos pueden anidarse; en el estado verás rutas como `module.vpc.module.subnets`. El anidamiento profundo complica depuración: evalúa si un nivel intermedio aporta claridad o solo indirección.

@section: terraform_remote_state

Cuando un stack necesita valores de otro (por ejemplo VPC ID desde un stack de red), puedes usar `data "terraform_remote_state"` apuntando al backend del otro proyecto. Requisitos:

* Permisos de lectura del estado remoto.
* **Contrato estable** de outputs; si el otro equipo renombra outputs, rompes el consumidor.

Documenta la dependencia entre equipos y versiona cambios coordinados.

@section: Testing y calidad en CI

En pull requests suele ejecutarse:

* `terraform fmt -check`
* `terraform validate`
* linters como **tflint**
* scanners de política como **tfsec**, **checkov** o **trivy** contra configuración

Para módulos compartidos, considera pruebas de integración en cuenta **sandbox** destruible (apply + destroy) en noches o en pipelines largos.

@section: Errores frecuentes

* Cambiar `source` o `version` de un módulo sin leer notas de migración (inputs renombrados).
* Colisiones de nombres globales (nombres de bucket S3) entre entornos sin prefijos o sufijos.
* Depender de outputs no documentados leyendo el state ajeno con trucos frágiles.
* Duplicar lógica entre módulos en lugar de parametrizar uno solo, generando divergencia.

@section: Patrones de diseño

* **Módulo de composición:** orquesta submódulos para un producto («plataforma web»).
* **Módulo mínimo:** una pieza (SG, bucket con políticas) reutilizable en muchos stacks.
* **Ambientes:** mismo módulo con `tfvars` distintos o workspaces; coherencia en nombres y tags.

@section: Laboratorio sugerido

1. Toma un `main.tf` monolítico de ejemplo y extrae la parte de red a `modules/vpc/`.
2. Parametriza CIDR y zonas de disponibilidad; expone `vpc_id` y subnet IDs por output.
3. Publica un tag `v0.1.0` en un repositorio de prueba y consume el módulo con `git::` y `ref` fija.
4. Ejecuta `plan` y verifica que no haya sorpresas respecto al monolito.

@quiz: ¿Por qué se recomienda fijar `ref` (tag o commit) al usar módulos desde Git?
@option: Porque Git es más lento sin ref
@correct: Para builds reproducibles y evitar que un cambio en la rama principal rompa despliegues sin revisión
@option: Porque Terraform no permite source Git sin ref
