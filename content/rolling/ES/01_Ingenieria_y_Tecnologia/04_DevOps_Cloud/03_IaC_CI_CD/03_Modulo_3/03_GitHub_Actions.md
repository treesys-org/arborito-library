@title: GitHub Actions: workflows, jobs y secretos
@icon: 🐙
@description: YAML, runs, matrices, environments y OIDC hacia la nube.
@order: 3

# GitHub Actions: automatizar CI en el repositorio

**GitHub Actions** ejecuta workflows definidos en YAML bajo `.github/workflows/` cuando ocurren eventos (`push`, `pull_request`, `schedule`, `workflow_dispatch`). Cada ejecución es un **run** con **jobs** (máquinas aisladas) y **steps** (comandos o acciones reutilizables).

@section: Anatomía de un workflow

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test
```

* **`on`:** disparadores.
* **`jobs`:** paralelos por defecto salvo `needs`.
* **`runs-on`:** tipo de runner (hosted o self-hosted).
* **`steps`:** secuencia en la misma VM.

@section: Contextos y secretos

* **`secrets.GITHUB_TOKEN`:** token efímero con permisos limitados para el repo.
* **Secrets del repo:** `secrets.MY_API_KEY` definidos en Settings → Secrets.
* **Variables** no sensibles: `vars` a nivel de entorno u organización.

**Nunca** imprimas secretos en logs; GitHub enmascara patrones conocidos pero no confíes ciegamente.

@section: Matrices

Prueba en varias versiones o SO:

```yaml
strategy:
  matrix:
    node: [18, 20]
runs-on: ubuntu-latest
```

Reduce duplicación y aumenta cobertura.

@section: Caché

`actions/cache` para dependencias (`npm`, `pip`, `maven`). Define claves basadas en lockfiles para invalidar correctamente.

@section: Environments y aprobaciones

**Environments** (`production`) permiten **protection rules**: revisores requeridos, wait timers, secretos distintos. Útil para despliegues sensibles.

@section: OIDC a AWS/Azure/GCP

En lugar de claves de larga duración en el repo, configura **OIDC** para que el workflow asuma un rol en la nube con **audience** y **subject** restringidos (`repo:org/name:ref:refs/heads/main`). Reduce superficie de filtración.

@section: Reutilización

* **Composite actions** en el mismo repo.
* **Reusable workflows** llamados con `workflow_call`.

Centraliza lint y tests para muchos repos.

@section: Límites y coste

Los runners hosted tienen **minutos** según plan; jobs largos o matrices grandes consumen cuota. **Self-hosted runners** dan control pero implican **hardening** (no expongas runners a PRs públicos sin aislamiento).

@section: Errores frecuentes

* Usar `pull_request` desde forks sin permisos para secretos (by design).
* `chmod` o rutas que solo existen en un SO de la matrix.
* Acciones de terceros sin pin de versión (`@v4` vs commit SHA para máxima inmutabilidad).

@section: Laboratorio sugerido

1. Crea un workflow que ejecute `pytest` o `npm test` en CI.
2. Añade caché de dependencias y mide el tiempo antes/después.
3. Lee la documentación de OIDC para tu proveedor cloud y dibuja el flujo de asunción de rol.

@quiz: ¿Qué ventaja principal ofrece OIDC frente a almacenar ACCESS_KEY en el repo?
@option: OIDC elimina la necesidad de Git
@correct: Credenciales de corta duración y alcance limitado, sin secretos estáticos en el repositorio
@option: OIDC solo funciona en repositorios privados
