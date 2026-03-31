@title: Terraform Cloud / Enterprise: Teams and Policies
@icon: ☁️
@description: Remote workspaces, runs, sensitive variables, and policy as code (Sentinel or OPA).
@order: 5

# Terraform Cloud and how teams work together

**Terraform Cloud (TFC)** and **Terraform Enterprise (TFE)** provide remote Terraform execution, managed state, workspace-level permissions, sensitive variables, **run** history, and **policy as code** integration. UI labels evolve; what matters is the **mental model**: code in Git, automatic plans, controlled applies, and policies before touching sensitive accounts.

@section: Remote workspace vs local execution

Locally you run `terraform plan` and `apply` on your machine with local credentials. On TFC/TFE **runs** execute on managed agents or **self-hosted Terraform Agents** when policy requires it. Code arrives from GitHub, GitLab, Bitbucket, or via CLI/API.

Common advantages:

* Centralized state and secrets with team-scoped access control.
* Auditable history of plans and applies.
* Optional **manual approval** before production `apply`.
* Integration with policies (Sentinel, OPA, or others) that block unsafe changes.

@section: Version-control flow

A typical flow:

1. Feature branch with `.tf` changes.
2. Pull request triggers a **speculative plan** (read-only plan against the linked workspace) for PR feedback.
3. After review, merge to the branch configured for the workspace.
4. New run on the workspace; if policies pass and approval rules are satisfied, `apply` is allowed (automatic or explicit click depending on settings).

**Run triggers** between workspaces chain dependencies (e.g. network → compute). Use carefully: poorly designed chains cause applies in the wrong order or trigger loops.

@section: Variables and secrets

In the TFC/TFE UI or API you define workspace variables:

* **Terraform variables** (implicit `TF_VAR_` prefix in the platform) or **environment** variables for providers.
* Mark **sensitive** what must not appear in UI logs.

Good practices:

* Rotate credentials after exposure or on a schedule.
* Prefer **OIDC** to AWS/Azure/GCP instead of long-lived keys when supported.
* Separate workspaces per environment (dev/stage/prod) with different permissions.

@section: Policy as code

**Sentinel** (HashiCorp ecosystem) or other integrations can **deny** plans that:

* Create instances without mandatory tags (`Environment`, `CostCenter`).
* Open `0.0.0.0/0` on sensitive security groups.
* Use disallowed instance types or regions.

Policies are **guardrails**: they do not replace human review, load testing, or vulnerability scanning for images; they reduce classic PR mistakes.

@section: Comparison with “S3 + CI only”

| Aspect | TFC/TFE | S3 backend + GitHub Actions |
|--------|---------|----------------------------|
| State | HashiCorp-managed | Your bucket and IAM policies |
| Execution | Managed or self-hosted agents | Runners you maintain |
| Policies | Native Sentinel/OPA integration | Install tools in CI |
| Cost | License/subscription | CI infra + engineer time |

Many teams start with S3 + CI and move to TFE when they need **centralized governance** and strong approvals.

@section: When to consider on-prem or not using TFC

* Regulatory requirements that data cannot leave your network.
* Corporate policy against SaaS for credentials.
* Cost: evaluate whether the value exceeds a well-built in-house pipeline.

@section: Operational mistakes

* Workspace pointing at the wrong branch and silent applies to the wrong environment.
* Duplicate secrets between workspace and repository (confusing source of truth).
* Policies so strict they block legitimate work without a documented exception process.

@section: Suggested lab

1. Create a trial Terraform Cloud account (free tier if available).
2. Connect a Git repository with a minimal cloud provider test project.
3. Configure a workspace, run a plan job, and read the report.
4. (Optional) Add a sample policy requiring an `Environment` tag on compute resources and observe a controlled failure when it is missing.

@quiz: What is the main advantage of Terraform Cloud over running Terraform only on a laptop?
@option: It makes HCL unnecessary
@correct: Centralized execution, managed state, run history, and team access controls
@option: It removes the need for cloud providers
