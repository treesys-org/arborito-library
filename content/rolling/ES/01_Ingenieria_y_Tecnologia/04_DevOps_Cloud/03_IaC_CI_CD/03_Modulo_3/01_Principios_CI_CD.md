@title: Principios de CI/CD
@icon: 📜
@description: Integración continua, entrega continua, despliegue continuo y gates de calidad.
@order: 1

# Principios de CI/CD: vocabulario que el equipo debe compartir

**CI/CD** no es una herramienta concreta: es un conjunto de **prácticas** para integrar cambios de código de forma frecuente, validarlos automáticamente y desplegarlos con trazabilidad. Esta lección separa conceptos que se usan como sinónimos (integración, entrega, despliegue) y conecta el flujo con ramas, pipelines y riesgo operativo.

@section: Integración continua (CI)

**Integración continua** significa que cada cambio que entra al repositorio principal (o que se propone en PR) **dispara** una compilación y una batería de pruebas automáticas. El objetivo es detectar fallos **rápido**, cuando el diff es pequeño y el contexto fresco.

Prácticas típicas:

* `main` siempre debe estar **verde** (o el equipo para de integrar hasta arreglarlo).
* Builds reproducibles (mismas versiones de dependencias, lockfiles).
* Feedback en minutos, no horas.

**CI no es** «un Jenkins encendido»: sin disciplina de commits pequeños y tests, el pipeline solo automatiza el caos.

@section: Entrega continua (CD) vs despliegue continuo

**Entrega continua (Continuous Delivery):** el software está **siempre en un estado desplegable**; el despliegue a producción es un **evento de negocio** (botón, aprobación), no un proyecto de meses.

**Despliegue continuo (Continuous Deployment):** cada cambio que pasa las pruebas **se promueve automáticamente** a producción sin paso manual. Requiere:

* Tests muy fiables.
* Observabilidad fuerte.
* Cultura de rollback y feature flags.

Muchos equipos hacen **Delivery** sin **Deployment** automático; la distinción importa para SLAs y compliance.

@section: Pipeline como contrato

Un pipeline típico incluye etapas como:

1. **Lint** y formato estático.
2. **Unit tests**.
3. **Build** de artefacto (binario, imagen OCI).
4. **Análisis de seguridad** (SCA, SAST según madurez).
5. **Pruebas de integración** contra entornos efímeros.
6. **Promoción** a entornos superiores con validaciones adicionales.

Cada etapa es un **gate**: si falla, no avanzas. Documenta qué gates son obligatorios para release.

@section: Ramas y estrategias

* **Trunk-based development:** cambios pequeños a `main` frecuentemente; feature flags para trabajo largo.
* **GitFlow:** más ramas y merges; más fricción para CI rápido en algunos equipos.

No existe una única estrategia «correcta»; la que importa es que **el pipeline** refleje el riesgo de tu dominio (finanzas vs. blog).

@section: Infraestructura y configuración

CI/CD aplica también a **infraestructura como código** y a **manifiestos de Kubernetes**: mismos principios de revisión, plan (`terraform plan`) y aplicación controlada.

@section: Métricas útiles (DORA)

Indicadores como **frecuencia de despliegue**, **lead time**, **tasa de fallo de cambio** y **tiempo de recuperación** ayudan a medir si el proceso mejora. No son fines en sí; evitan optimizar solo «velocidad de build» ignorando estabilidad.

@section: Errores frecuentes

* Confiar en pruebas manuales repetitivas como sustituto de automatización.
* Pipelines que tardan horas y desincentivan integración frecuente.
* Despliegues «big bang» los viernes sin runbook ni rollback.

@section: Laboratorio sugerido

1. Dibuja en papel el pipeline ideal para un servicio que conoces (etapas y gates).
2. Para cada etapa, escribe qué fallo detectaría y qué haría el equipo si falla.
3. Compara con **Delivery** vs **Deployment** y decide cuál encaja con tu contexto de riesgo.

@quiz: ¿Qué distingue principalmente Continuous Delivery de Continuous Deployment?
@option: CD siempre requiere Kubernetes
@correct: En Delivery el despliegue a producción puede ser manual o aprobado; en Deployment cada cambio aprobado se despliega automáticamente
@option: No hay diferencia, son marcas
