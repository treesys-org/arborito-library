@title: CI/CD Principles
@icon: 📜
@description: Continuous integration, delivery, deployment, and quality gates.
@order: 1

# CI/CD principles: vocabulary the team must share

**CI/CD** is not a single tool: it is a set of **practices** to integrate code changes frequently, validate them automatically, and deploy with traceability. This lesson separates concepts often used as synonyms (integration, delivery, deployment) and connects the flow to branches, pipelines, and operational risk.

@section: Continuous integration (CI)

**Continuous integration** means every change landing in the main branch (or proposed in a PR) **triggers** a build and an automated test suite. The goal is to detect failures **early**, when the diff is small and context is fresh.

Typical practices:

* `main` should stay **green** (or the team stops integrating until fixed).
* Reproducible builds (same dependency versions, lockfiles).
* Feedback in minutes, not hours.

**CI is not** “a Jenkins server turned on”: without small commits and tests, the pipeline only automates chaos.

@section: Continuous delivery vs continuous deployment

**Continuous Delivery:** software is **always in a deployable state**; production deployment is a **business event** (button, approval), not a months-long project.

**Continuous Deployment:** every change that passes tests **automatically promotes** to production without a manual step. Requires:

* Highly reliable tests.
* Strong observability.
* A culture of rollback and feature flags.

Many teams practice **Delivery** without automatic **Deployment**; the distinction matters for SLAs and compliance.

@section: The pipeline as a contract

A typical pipeline includes stages such as:

1. **Lint** and static formatting.
2. **Unit tests**.
3. **Build** artifact (binary, OCI image).
4. **Security analysis** (SCA, SAST depending on maturity).
5. **Integration tests** against ephemeral environments.
6. **Promotion** to higher environments with additional validations.

Each stage is a **gate**: if it fails, you do not advance. Document which gates are mandatory for release.

@section: Branches and strategies

* **Trunk-based development:** frequent small changes to `main`; feature flags for long work.
* **GitFlow:** more branches and merges; heavier friction for fast CI in some teams.

There is no single “correct” strategy; the pipeline should reflect your domain’s risk (finance vs blog).

@section: Infrastructure and configuration

CI/CD also applies to **infrastructure as code** and **Kubernetes manifests**: same review principles, `terraform plan`, and controlled application.

@section: Useful metrics (DORA)

Indicators such as **deployment frequency**, **lead time**, **change failure rate**, and **time to restore** help measure whether the process improves. They are not ends in themselves; avoid optimizing only “build speed” while ignoring stability.

@section: Common mistakes

* Relying on repetitive manual tests instead of automation.
* Pipelines that take hours and discourage frequent integration.
* Friday “big bang” deployments without runbooks or rollback.

@section: Suggested lab

1. Sketch your ideal pipeline for a service you know (stages and gates).
2. For each stage, write what failure it would catch and what the team would do if it fails.
3. Compare **Delivery** vs **Deployment** and decide which fits your risk context.

@quiz: What mainly distinguishes Continuous Delivery from Continuous Deployment?
@option: CD always requires Kubernetes
@correct: In Delivery, production deployment may be manual or approved; in Deployment every approved change deploys automatically
@option: There is no difference — they are marketing terms
