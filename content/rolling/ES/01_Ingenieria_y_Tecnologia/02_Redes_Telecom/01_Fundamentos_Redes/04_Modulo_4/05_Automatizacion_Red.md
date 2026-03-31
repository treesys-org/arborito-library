@title: Automatización de red: APIs, IaC y GitOps
@icon: 🤖
@description: NETCONF/RESTCONF, Ansible, plantillas Jinja y pruebas de cambio.
@order: 5

# Automatización de redes: menos CLI a mano, más revisiones

La **automatización** reduce errores humanos y acelera cambios auditables: **APIs** (`NETCONF/RESTCONF`, REST vendor), **Ansible**, **Terraform** (para cloud networking), **GitOps** para declarar políticas. Esta lección lista **patrones** y **riesgos** (credenciales, drift).

@section: APIs modernas

**NETCONF** sobre SSH con datos **XML/YANG**; **RESTCONF** HTTP-friendly. Ventaja: modelos estructurados vs scraping CLI.

**gNMI** para streaming telemetry a **Prometheus/Grafana**.

@section: Ansible para red

Módulos `ios`, `nxos`, `junos` aplican configuración idempotente con **Jinja2** templates por rol (core/distribution/access).

**Inventory** dinámico desde CMDB o NetBox.

@section: Infraestructura como código

Para **VPC/VPN** en cloud, Terraform + pipelines. **Drift detection** periódico.

@section: Pruebas

**Batfish**, **pyATS** validan cambios antes de producción; **canary** en dispositivos piloto.

@section: Seguridad

**RBAC** en controladores, **vault** para credenciales, **audit logs** de cambios.

@section: Errores frecuentes

* Automatizar sin **rollback** conocido.
* Mezclar fuentes de verdad (CLI manual + Git).

@section: Laboratorio sugerido

1. Ejecuta playbook Ansible de solo lectura (`gather_facts`) contra IOS-XE en GNS3/EVE-NG.
2. Versiona plantillas Jinja en Git con PR obligatorio.
3. Diseña pipeline: lint → simulación → ventana de cambio.

@quiz: ¿Qué ventaja principal aporta NETCONF/YANG frente a scripts CLI por expect?
@option: Siempre es más rápido en runtime
@correct: Modelos estructurados y transacciones más predecibles que scraping de texto
@option: Elimina la necesidad de routing
