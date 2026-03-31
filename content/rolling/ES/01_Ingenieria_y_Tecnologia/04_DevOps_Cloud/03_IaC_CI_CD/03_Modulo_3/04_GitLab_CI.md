@title: GitLab CI: .gitlab-ci.yml, runners y stages
@icon: 🦊
@description: Pipelines, reglas, artifacts y integración con Kubernetes.
@order: 4

# GitLab CI/CD: pipelines declarativos

En **GitLab**, el archivo `.gitlab-ci.yml` define **stages**, **jobs** y **scripts**. Los **runners** ejecutan los jobs (compartidos GitLab.com o **self-managed** en tu infra). Esta lección cubre el modelo mental, reglas, **artifacts** y patrones de despliegue.

@section: Estructura de pipeline

```yaml
stages:
  - test
  - build
  - deploy

variables:
  IMAGE_TAG: $CI_COMMIT_SHORT_SHA

test_unit:
  stage: test
  script:
    - npm test

build_image:
  stage: build
  script:
    - docker build -t myapp:$IMAGE_TAG .
```

**Stages** ejecutan en orden; jobs del mismo stage **en paralelo** salvo que uses `needs` para DAG.

@section: Reglas y solo:merge_requests

* **`rules:`** define cuándo ejecutar un job (ramas, tags, cambios en paths).
* **`only/except`** es legado; preferir `rules` en proyectos nuevos.

Ejemplo: ejecutar job pesado solo en `main` o en tags de release.

@section: Runners

* **Shared runners** en GitLab.com (cuotas según plan).
* **Specific runners** registrados en tu infra (Docker, shell, Kubernetes executor).

**Tags** asignan jobs a runners con capacidades (GPU, Docker, mayor disco).

**Seguridad:** runners que ejecutan código de forks públicos deben estar **aislados**; no montes secretos de producción en esos runners.

@section: Artifacts y cache

* **`artifacts:`** pasan archivos entre stages (binarios, reportes de cobertura).
* **`cache:`** aceleran dependencias entre pipelines (clave por branch/lockfile).

Define **expiración** de artifacts para no llenar almacenamiento.

@section: Integración con contenedores y registry

GitLab incluye **Container Registry** por proyecto. Jobs típicos:

1. `docker build` + `docker push` con credenciales del CI (`CI_JOB_TOKEN` o deploy token).
2. Despliegue en Kubernetes con manifiestos o Helm.

@section: Environments y aprobaciones

**Environments** (`production`, `staging`) pueden requerir **manual jobs** (`when: manual`) y **approvals** en GitLab Premium/Ultimate según licencia.

**Protected branches** y **protected environments** limitan quién puede desplegar.

@section: Child pipelines y includes

* **`include:`** reutiliza plantillas de otros archivos o proyectos.
* **Parent/child pipelines** dividen pipelines grandes para claridad y paralelismo.

@section: Secretos

* **CI/CD variables** en Settings → masked, protected.
* **External secrets** (Vault, cloud secret managers) vía integraciones o scripts.

**Nunca** imprimas variables masked en echo de formas que bypassen el mask.

@section: Errores frecuentes

* Jobs que dependen de servicios sin `services:` (por ejemplo base de datos en Docker).
* `needs` mal configurado rompiendo orden de stages.
* Runners sin Docker para jobs que asumen `image: docker:24`.

@section: Laboratorio sugerido

1. Crea un proyecto GitLab (o usa instancia de prueba) con `.gitlab-ci.yml` mínimo.
2. Añade `artifacts` con un reporte de tests (JUnit) y visualiza en la UI.
3. Configura un job manual de despliegue a un entorno `staging`.

@quiz: ¿Qué permite el keyword `needs` en GitLab CI?
@option: Eliminar stages por completo
@correct: Definir dependencias entre jobs para DAG y saltar orden estricto de stages cuando aplica
@option: Solo ejecutar en forks
