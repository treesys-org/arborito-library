@title: Terraform Modules: Composition and Reuse
@icon: 📦
@description: module {}, local and registry sources, versioning, and design patterns.
@order: 4

# Terraform modules: compose without copy-paste

A **module** is a directory of `.tf` files that encapsulates related resources (network, databases, Kubernetes, etc.). Modules avoid duplicating hundreds of lines and let you version **stable interfaces** (`variables` + `outputs`) while internals evolve. This lesson covers invocation, sources, contracts, and mistakes you see in real projects.

@section: Calling a module (root module)

```hcl
module "vpc" {
  source = "./modules/vpc"

  cidr_block = "10.0.0.0/16"
  azs        = ["eu-west-1a", "eu-west-1b"]
}

output "vpc_id" {
  value = module.vpc.vpc_id
}
```

The block **name** (`vpc`) is local to the stack. Module outputs are exposed as `module.vpc.<output_name>`. Resources inside are namespaced in state like `module.vpc.aws_vpc.this`.

@section: Module sources

* **Local path:** `source = "./modules/network"` — fast in a monorepo; changes and PRs together.
* **Git with fixed ref:**  
  `source = "git::https://github.com/org/tf-modules.git//vpc?ref=v1.2.0"`  
  Pin **tags** or **commit SHAs**; avoid floating `main` in production.
* **Public or private registry:** e.g. `terraform-aws-modules/vpc/aws` with `version = "~> 5.0"`.

Practical rule: if the module is not yours, **pin** versions and read the CHANGELOG before upgrading majors.

@section: Module contract: inputs and outputs

A good module documents:

* Required and optional **inputs:** types, defaults, `validation` rules.
* **Outputs:** only what consumers need; do not leak secrets or irrelevant internal IDs.
* **Assumptions:** e.g. “creates only public subnets” or “requires sufficient EIP quota.”

Anti-pattern: a “god” module with dozens of mandatory variables without sensible defaults; prefer composing smaller modules.

@section: Root vs child modules and nesting

The **root module** is the directory from which you run `terraform apply`. Child modules can nest; state paths look like `module.vpc.module.subnets`. Deep nesting complicates debugging — evaluate whether an intermediate layer adds clarity or only indirection.

@section: terraform_remote_state

When one stack needs values from another (e.g. VPC ID from a network stack), you can use `data "terraform_remote_state"` pointing at the other project’s backend. Requirements:

* Read permission on the remote state.
* A **stable** output contract; if the other team renames outputs, consumers break.

Document cross-team dependencies and coordinate versioned changes.

@section: Testing and quality in CI

Pull requests often run:

* `terraform fmt -check`
* `terraform validate`
* linters like **tflint**
* policy scanners like **tfsec**, **checkov**, or **trivy** against configuration

For shared modules, consider integration tests in a **disposable** sandbox account (apply + destroy) on a schedule or in long pipelines.

@section: Common mistakes

* Changing `source` or `version` without reading migration notes (renamed inputs).
* Global name collisions (S3 bucket names) across environments without prefixes or suffixes.
* Depending on undocumented outputs by reading someone else’s state in fragile ways.
* Duplicating logic between modules instead of parameterizing one module, causing divergence.

@section: Design patterns

* **Composition module:** orchestrates submodules for a product (“web platform”).
* **Minimal module:** one reusable piece (SG, bucket with policies) used in many stacks.
* **Environments:** same module with different `tfvars` or workspaces; keep naming and tags consistent.

@section: Suggested lab

1. Take a monolithic example `main.tf` and extract networking into `modules/vpc/`.
2. Parameterize CIDR and AZs; expose `vpc_id` and subnet IDs via outputs.
3. Publish a `v0.1.0` tag in a test module repo and consume it via `git::` with a fixed `ref`.
4. Run `plan` and verify nothing surprising versus the monolith.

@quiz: Why pin `ref` (tag or commit) when using modules from Git?
@option: Git is slower without ref
@correct: For reproducible builds and to avoid unreviewed changes on the default branch breaking deployments
@option: Terraform does not allow Git sources without ref
