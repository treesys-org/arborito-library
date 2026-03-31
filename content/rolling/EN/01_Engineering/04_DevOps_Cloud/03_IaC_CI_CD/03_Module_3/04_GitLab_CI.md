@title: GitLab CI: .gitlab-ci.yml, Runners, and Stages
@icon: 🦊
@description: Pipelines, rules, artifacts, and Kubernetes integration.
@order: 4

# GitLab CI/CD: declarative pipelines

In **GitLab**, the `.gitlab-ci.yml` file defines **stages**, **jobs**, and **scripts**. **Runners** execute jobs (shared GitLab.com runners or **self-managed** on your infra). This lesson covers the mental model, rules, **artifacts**, and deployment patterns.

@section: Pipeline structure

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

**Stages** run in order; jobs in the same stage run **in parallel** unless `needs` builds a DAG.

@section: Rules and merge requests

* **`rules:`** defines when a job runs (branches, tags, path changes).
* **`only/except`** are legacy; prefer `rules` in new projects.

Example: run heavy jobs only on `main` or release tags.

@section: Runners

* **Shared runners** on GitLab.com (quotas depend on plan).
* **Specific runners** registered on your infra (Docker, shell, Kubernetes executor).

**Tags** assign jobs to runners with capabilities (GPU, Docker, larger disk).

**Security:** runners executing code from public forks must be **isolated**; do not mount production secrets on those runners.

@section: Artifacts and cache

* **`artifacts:`** pass files between stages (binaries, coverage reports).
* **`cache:`** speeds up dependencies between pipelines (key per branch/lockfile).

Define **expiration** for artifacts to avoid filling storage.

@section: Containers and registry

GitLab includes a per-project **Container Registry**. Typical jobs:

1. `docker build` + `docker push` with CI credentials (`CI_JOB_TOKEN` or deploy token).
2. Deploy to Kubernetes with manifests or Helm.

@section: Environments and approvals

**Environments** (`production`, `staging`) can require **manual jobs** (`when: manual`) and **approvals** (GitLab Premium/Ultimate depending on license).

**Protected branches** and **protected environments** limit who can deploy.

@section: Child pipelines and includes

* **`include:`** reuses templates from other files or projects.
* **Parent/child pipelines** split large pipelines for clarity and parallelism.

@section: Secrets

* **CI/CD variables** under Settings → masked, protected.
* **External secrets** (Vault, cloud secret managers) via integrations or scripts.

**Never** echo masked variables in ways that bypass masking.

@section: Common mistakes

* Jobs depending on services without `services:` (e.g. database in Docker).
* Misconfigured `needs` breaking stage ordering.
* Runners without Docker for jobs that assume `image: docker:24`.

@section: Suggested lab

1. Create a GitLab project (or use a test instance) with a minimal `.gitlab-ci.yml`.
2. Add `artifacts` with a JUnit test report and view it in the UI.
3. Configure a manual deploy job to a `staging` environment.

@quiz: What does the `needs` keyword enable in GitLab CI?
@option: Remove stages entirely
@correct: Define job dependencies for a DAG and skip strict stage ordering when applicable
@option: Run only on forks
