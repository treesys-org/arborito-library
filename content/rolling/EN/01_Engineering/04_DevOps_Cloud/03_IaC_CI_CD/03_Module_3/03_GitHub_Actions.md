@title: GitHub Actions: Workflows, Jobs, and Secrets
@icon: 🐙
@description: YAML, runs, matrices, environments, and OIDC to the cloud.
@order: 3

# GitHub Actions: automate CI in the repository

**GitHub Actions** runs workflows defined in YAML under `.github/workflows/` when events occur (`push`, `pull_request`, `schedule`, `workflow_dispatch`). Each run is a **run** with **jobs** (isolated machines) and **steps** (commands or reusable actions).

@section: Workflow anatomy

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

* **`on`:** triggers.
* **`jobs`:** parallel by default unless `needs`.
* **`runs-on`:** runner type (hosted or self-hosted).
* **`steps`:** sequence on the same VM.

@section: Contexts and secrets

* **`secrets.GITHUB_TOKEN`:** short-lived token with limited repo permissions.
* **Repository secrets:** `secrets.MY_API_KEY` defined under Settings → Secrets.
* **Variables** for non-sensitive values at environment or organization level.

**Never** print secrets in logs; GitHub masks known patterns but do not rely on that blindly.

@section: Matrices

Test across versions or OS:

```yaml
strategy:
  matrix:
    node: [18, 20]
runs-on: ubuntu-latest
```

Reduces duplication and increases coverage.

@section: Cache

`actions/cache` for dependencies (`npm`, `pip`, `maven`). Define keys based on lockfiles for correct invalidation.

@section: Environments and approvals

**Environments** (`production`) support **protection rules**: required reviewers, wait timers, distinct secrets. Useful for sensitive deployments.

@section: OIDC to AWS/Azure/GCP

Instead of long-lived keys in the repo, configure **OIDC** so the workflow assumes a cloud role with restricted **audience** and **subject** (`repo:org/name:ref:refs/heads/main`). Reduces leakage surface.

@section: Reuse

* **Composite actions** in the same repo.
* **Reusable workflows** invoked with `workflow_call`.

Centralize lint and tests across many repos.

@section: Limits and cost

Hosted runners have **minute** quotas by plan; long jobs or large matrices consume quota. **Self-hosted runners** give control but require **hardening** (do not expose runners to public forks without isolation).

@section: Common mistakes

* Using `pull_request` from forks without secrets permissions (by design).
* `chmod` or paths that exist only on one OS in the matrix.
* Third-party actions without version pins (`@v4` vs commit SHA for maximum immutability).

@section: Suggested lab

1. Create a workflow that runs `pytest` or `npm test` in CI.
2. Add dependency caching and measure time before/after.
3. Read OIDC docs for your cloud provider and sketch the role assumption flow.

@quiz: What is the main advantage of OIDC over storing ACCESS_KEY in the repo?
@option: OIDC removes the need for Git
@correct: Short-lived, narrowly scoped credentials without static secrets in the repository
@option: OIDC only works in private repositories
