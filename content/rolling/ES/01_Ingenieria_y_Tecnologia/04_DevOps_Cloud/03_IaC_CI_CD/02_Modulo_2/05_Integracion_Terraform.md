@title: Integración Terraform + Ansible: división de responsabilidades
@icon: 🔗
@description: Terraform para la nube, Ansible para el SO; remote-exec y provisioners.
@order: 5

# Terraform y Ansible juntos: quién hace qué

En muchos equipos **Terraform** crea la infraestructura (VPC, subredes, instancias, balanceadores, security groups) y **Ansible** configura el sistema operativo (paquetes, usuarios, servicios, ficheros). Esta separación es sana: cada herramienta brilla en su dominio y reduce el acoplamiento entre capa de proveedor y capa de máquina. Esta lección también explica por qué los **provisioners** de Terraform suelen ser una mala idea para configuración compleja.

@section: Patrón recomendado

1. `terraform apply` crea instancias con **tags** o **metadata** que Ansible usará como criterio de inventario dinámico.
2. El pipeline (o un job manual) ejecuta Ansible contra el inventario filtrado (por ejemplo `Role=web` y `Env=prod`).

**Evita** `remote-exec` en Terraform para instalar aplicaciones largas: es difícil de depurar, no es idempotente por defecto y mezcla responsabilidades.

@section: Inventario dinámico desde cloud

Plugins de inventario para AWS (`aws_ec2`), Azure, GCP generan grupos automáticamente a partir de etiquetas. Terraform etiqueta recursos; Ansible los descubre sin listas manuales.

Ejemplo conceptual: Terraform asigna `tags = { Role = "app", Env = "staging" }`; Ansible limita con `-l` o grupos derivados del plugin.

@section: Orden de ejecución y dependencias

Ansible necesita **conectividad**: SSH abierto, usuario bootstrap, claves o SSM. Si el `apply` de Terraform acaba de crear la VM, puede haber un retardo hasta que el **cloud-init** termine. Usa:

* `wait_for` en Ansible para puerto 22.
* Health checks del proveedor antes de marcar el apply como exitoso en pipelines.

**Outputs de Terraform:** exporta IPs o DNS en `output` y pásalos al playbook con `-e` o genera un inventario intermedio. **terraform_remote_state** en otro stack puede alimentar variables de Ansible si el flujo está documentado.

@section: Alternativa: cloud-init para bootstrap mínimo

`user_data` puede instalar Python, usuario de despliegue y claves SSH; el resto de configuración en Ansible. Mantén cloud-init **mínimo** para no duplicar lógica en dos lugares.

@section: Cuándo usar solo Terraform

* Imágenes **inmutables** (Packer) y sin configuración post-boot por SSH.
* Servicios totalmente gestionados (RDS, Lambda) sin SO a tunear.

@section: Cuándo usar solo Ansible

* Infraestructura ya existente importada o ajena a tu Terraform.
* Entornos on-prem sin API de nube unificada.

@section: Antipatrón: provisioners en Terraform

Los provisioners `remote-exec` y `local-exec` en Terraform:

* Se ejecutan en momentos específicos del ciclo de vida y pueden fallar de forma opaca.
* No sustituyen un modelo de idempotencia de Ansible.
* Complican el `taint` y el reprocesamiento.

Reserva `local-exec` para **hooks** cortos (notificar un sistema externo) si realmente necesitas.

@section: Seguridad

* No incrustes claves SSH en Terraform en claro; usa mecanismos del proveedor (metadata, IAM roles para SSM, etc.).
* Limita qué puede ejecutar el pipeline de Ansible en producción (inventario, tags, aprobaciones).

@section: Laboratorio sugerido

1. Crea una VM con Terraform y etiquétala `App=demo` y `Env=dev`.
2. Configura `ansible-inventory` con el plugin de tu nube y verifica que la VM aparece en el grupo esperado.
3. Ejecuta `ansible -m ping` contra ese grupo.
4. Documenta en un README de un solo párrafo el orden: apply → espera → ansible.

@quiz: ¿Por qué se desaconseja el provisioner remote-exec de Terraform para configuración compleja?
@option: Porque Terraform no soporta SSH
@correct: Porque mezcla aprovisionamiento de infraestructura con configuración de SO, es difícil de depurar y repetir de forma idempotente
@option: Porque Ansible no puede usar inventario dinámico
