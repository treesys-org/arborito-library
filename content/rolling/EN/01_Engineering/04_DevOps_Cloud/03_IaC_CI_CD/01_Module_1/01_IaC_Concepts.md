@title: IaC Concepts and Cloud Providers
@icon: 📜
@description: Declarative vs imperative, idempotence, state, and the right mental model for infrastructure as code.
@order: 1

# Infrastructure as code: the right mental model

Before you write a single `.tf` file or playbook, you need **shared vocabulary** with your team and your tools. This lesson pins down definitions that, when misunderstood, cause incidents: “I applied the change but it reverted,” “state does not match,” “it works on my machine.” The goal is to explain **why** you version infrastructure and **how** that ties to Git, pipelines, and audits.

@section: What IaC is (and is not)

**Infrastructure as code (IaC)** is the practice of describing resources (networks, machines, load balancers, managed databases, permissions, DNS, certificates) in **versioned files** and applying changes through **repeatable processes** (CLI, pipelines, peer review policies, and often explicit approvals).

What IaC is **not**:

* A bash script that only runs commands in order without a state model (that is fragile **imperative** automation if it fails halfway or is re-run carelessly).
* Clicking around the cloud console without a trace in Git (no traceability or review).
* A CI job that calls APIs without declaring an explicit **desired state** or documenting impact.

The key difference: IaC assumes the tooling has a **model** (even if implicit in the engine) of “how the world should look” versus “how it looks now.” That model may live in HCL files, CloudFormation YAML, Kubernetes manifests, or idempotent playbooks.

@section: Imperative vs declarative

In an **imperative** approach you list *steps*: “create VPC, create subnet, open port 443.” If something fails halfway, the system is left in a partial state and the next attempt may collide with partially created resources.

In a **declarative** approach you describe the *outcome*: “I want a VPC with these subnets, route tables, and firewall rules.” The engine (Terraform, CloudFormation, Pulumi with a declarative model, etc.) computes a **plan of differences** between known state and desired state.

**Terraform** is mostly declarative: you declare resources and dependencies; the engine proposes `plan` then `apply`.  
**Ansible** is usually **imperative** in execution (ordered tasks), but well-designed modules are **idempotent** and converge toward a desired state without accumulating side effects.

In practice many teams combine: Terraform (or other cloud IaC) for the **provider layer**, Ansible or immutable images for **OS configuration**, and Kubernetes for **workloads**. The boundary is not dogmatic; what matters is knowing the **source of truth** for each layer.

@section: Idempotence

An operation is **idempotent** if running it many times leaves the same result as running it once (except for external effects such as user-generated data).

Bad example: always `echo "foo" >> /etc/app.conf` — not idempotent (the file grows).

Good example: a `copy` or `lineinfile`-style module that guarantees **exactly** the desired line.

**Why it matters:** pipelines retry; playbooks rerun after failures; nodes are rebuilt. If your automation is not idempotent, each run may **accumulate junk**, duplicate rules, or corrupt configuration.

@section: State and drift

Most declarative tools need **state**: a map of cloud resource IDs, dependency metadata, and sometimes sensitive values. State may live:

* In a **local file** (`terraform.tfstate`) — fine for personal experiments, fragile for teams.
* In a **remote backend** (S3 with locking, Terraform Cloud, Azure Storage, etc.) with encryption and fine-grained permissions.

**Drift:** someone changed the console, an external script, or an API and state no longer matches reality. The next `plan` may propose **recreating** resources or **adjusting** attributes in surprising ways.

Common mitigation:

* Team policy: structural changes **only** via code and pipeline.
* IAM that limits who can edit critical resources in the console.
* Periodic scans or read-only `terraform plan` to detect drift.

@section: Providers and resources (Terraform)

In Terraform, a **provider** is a plugin that maps HCL blocks (`aws_instance`, `azurerm_linux_virtual_machine`) to API calls.

Each **resource** has a type, local name, and arguments. **Data sources** read existing objects without creating them (AMIs, VPCs by filter, DNS zones).

**Provider versions:** pin versions in `required_providers` so `terraform init` does not silently download a major that breaks schemas and pipelines weeks later.

@section: GitOps (operational view)

**GitOps** uses Git as the **operational source of truth**: accepted merges trigger reconciliation (Argo CD, Flux) or Terraform application in CI. Benefits: PRs show diffs, revert is a known operation, audits tie to commits.

You do not have to “do GitOps” to “do IaC”, but it is standard for teams that want **reviewed** changes before touching production.

@section: Quick comparison table

| Tool / approach | Typical strength | Watch out for |
|----------------|------------------|---------------|
| Terraform | Cloud APIs, modules, state | Drift if you mix in console changes |
| CloudFormation / Bicep | Native AWS/Azure, stacks | Vendor-specific model |
| Ansible | OS heterogeneity, no agent | Long unstructured playbooks |
| Pulumi | Real code (TS, Go…) | Steeper if you only wanted HCL |

@section: Mental lab

1. List three cloud resources you want versioned (VPC, SG, RDS, bucket).
2. For each: is there a stable API? Who can change it outside Git?
3. Write a one-sentence team rule, e.g. “Production SG changes only via PR and change window.”

@section: Common mistakes

* Treating IaC as a “one-off script” without `plan` or review.
* Sharing `tfstate` by email or without locking (corruption and conflict risk).
* Mixing environments in one state without workspaces or separate stacks.
* Assuming “declarative” means “no need to understand order”: implicit and explicit dependencies still matter.

@section: Closing

IaC is not magic: it is **discipline** to model the world, version the model, and apply it with tools that understand state and APIs. Next in this track is **HCL and Terraform** with hands-on code.

@quiz: What best distinguishes a typical declarative Terraform workflow from an imperative bash script?
@option: Bash is always faster
@correct: Declarative describes desired state and the engine computes a diff; imperative lists sequential steps
@option: There is no practical difference

@quiz: What is “drift” in IaC?
@option: Using Git instead of SVN
@correct: A mismatch between what code/state says and what is actually deployed in the provider
@option: Accidentally deleting state
