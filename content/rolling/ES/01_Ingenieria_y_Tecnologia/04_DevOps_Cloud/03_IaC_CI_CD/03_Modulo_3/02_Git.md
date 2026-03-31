@title: Git para pipelines: ramas, merges y hooks
@icon: 🌿
@description: Flujos de trabajo, revisiones, firmas y buenas prácticas para equipos.
@order: 2

# Git como base de CI/CD

**Git** es el sistema de control de versiones que hace posible revisar diffs, revertir cambios y disparar pipelines. Esta lección no repite el tutorial de `init/add/commit`, sino que conecta **operaciones** con **prácticas de equipo**: ramas protegidas, PR/MR, hooks y firmas.

@section: Conceptos esenciales

* **Commit:** snapshot inmutable con padre(s) y metadatos (autor, fecha, mensaje).
* **Branch:** puntero móvil a un commit; `main` suele ser la línea de integración.
* **Merge:** combina historiales; **rebase** reescribe commits para historia lineal (con cuidado en ramas compartidas).
* **Tag:** referencia fija a un commit (releases).

**Hash SHA** identifica el contenido exacto; los pipelines deben construir desde commits conocidos, no desde tags movibles sin control.

@section: Pull requests y revisión

El flujo estándar en equipos remotos:

1. Rama de feature desde `main`.
2. Commits atómicos con mensajes claros.
3. **Pull request** con descripción, checklist de pruebas, enlaces a tickets.
4. Revisión por pares; CI debe pasar antes de merge.
5. Merge (squash, merge commit o rebase según política).

**CODEOWNERS** (GitHub/GitLab) enruta revisiones a equipos por carpeta.

@section: Ramas protegidas

Configura la rama principal para:

* Requerir CI verde antes de merge.
* Requerir aprobaciones (1+).
* Impedir force-push y borrado accidental.

Esto reduce incidentes por cambios no revisados.

@section: Hooks locales y en servidor

* **pre-commit:** hooks locales antes de `git commit` (formato, lint, secret scanning).
* **server-side:** hooks en el servidor Git o reglas de branch para validar mensajes o firmas.

Los hooks **no sustituyen** CI: el entorno local puede saltarse hooks; el pipeline es la fuente de verdad.

@section: Firmas y supply chain

**GPG/SSH signing** de commits y tags verifica identidad. Para releases, **SLSA** y firmas de artefactos (cosign, sigstore) ganan tracción.

@section: Monorepo vs multirepo

* **Monorepo:** un repo con muchos servicios; herramientas como Bazel, Nx, Turborepo gestionan builds selectivos.
* **Multirepo:** un repo por servicio; CI más simple por repo pero más overhead de dependencias compartidas.

@section: Conflictos y resolución

* Conflictos de merge: entiende qué cambió en cada lado; no aceptes «theirs» a ciegas en producción.
* **Blame** y `git bisect` para localizar el commit que introdujo un fallo.

@section: Integración con CI

Los sistemas CI escuchan `push` y `pull_request`. Configura:

* Matrices de versión (Python 3.10, 3.11).
* Caché de dependencias con cuidado (invalidación por lockfile).
* Secretos en **settings** del repo, no en YAML público.

@section: Errores frecuentes

* Commits gigantes con cinco features mezcladas (imposible de revertir).
* Subir `.env` o claves (usa **git-secrets**, scanning en CI).
* Reescribir historia `main` compartida.

@section: Laboratorio sugerido

1. Crea un repo de prueba y una rama; abre PR con una pequeña modificación.
2. Configura una regla de rama que requiera CI (GitHub Actions mínimo).
3. Prueba `git bisect` con un bug simulado entre commits.

@quiz: ¿Por qué los hooks de pre-commit locales no bastan como única garantía de calidad?
@option: Porque Git no soporta hooks
@correct: Porque pueden omitirse o desactivarse; el pipeline en CI debe validar en un entorno controlado
@option: Porque solo funcionan en Windows
