@title: Terraform: HCL, Resources, and Variables
@icon: 🧱
@description: HCL syntax, resource/data blocks, variables, outputs, and the terraform init/plan/apply flow.
@order: 2

# Terraform in practice: HCL, resources, and variables

Terraform is a **declarative** engine that reads configuration in **HCL** (HashiCorp Configuration Language), computes a **plan** of changes against **state**, and applies those changes via provider APIs. This lesson covers what you need to read existing stacks, write new resources, and stay oriented when `plan` prints hundreds of lines in a real environment.

@section: Basic blocks

A `.tf` file combines blocks with different meanings:

* **`terraform`:** Terraform itself (`required_version`, `backend`, `required_providers`, occasional `experiments`).
* **`provider`:** credentials, default region, and features (often via environment variables like `AWS_PROFILE`).
* **`resource`:** create or update an object in the provider.
* **`data`:** read existing objects (read-only).
* **`variable`:** typed, parameterized input with validation.
* **`output`:** expose values after apply (IPs, DNS, ARNs) for humans or downstream systems.
* **`locals`:** derived values to avoid repeating long expressions.

Minimal illustrative example (generic resource names):

```hcl
terraform {
  required_version = ">= 1.5.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

variable "aws_region" {
  type    = string
  default = "eu-west-1"
}

variable "project" {
  type = string
}

resource "aws_s3_bucket" "logs" {
  bucket = "${var.project}-logs"
}

output "bucket_name" {
  value = aws_s3_bucket.logs.id
}
```

@section: References and meta-arguments

Inside a resource you can reference others:

* `aws_instance.web.id` — attributes exported after creation.
* `data.aws_ami.alpine.id` — data from an existing AMI.

Meta-arguments you should know:

* **`depends_on`:** explicit dependency when Terraform cannot infer order.
* **`count`:** N indexed instances (`resource.x[0]`).
* **`for_each`:** a map or set of instances with stable keys (often preferred over `count` when identity matters).
* **`lifecycle`:**  
  - `prevent_destroy` against accidental deletion of critical resources.  
  - `ignore_changes` to freeze attributes the provider changes outside Terraform.  
  - `create_before_destroy` for replace strategies with less downtime.

@section: CLI workflow

1. **`terraform init`:** download providers, configure state backend, prepare plugins.
2. **`terraform fmt`:** format files (wire it into pre-commit).
3. **`terraform validate`:** validate syntax and types without calling the cloud (fast CI).
4. **`terraform plan`:** compute and show the proposed diff — **review** before apply.
5. **`terraform apply`:** apply changes; in CI you often save a binary plan and apply only that artifact.

Safe CI pattern: `terraform plan -out=plan.tfplan` in one job; a later job runs `terraform apply plan.tfplan` after approval or merge.

@section: Variables: types and precedence

Typical precedence order (lowest to highest):

1. Default values in `variable` blocks.
2. `terraform.tfvars` / `*.auto.tfvars`.
3. `-var` and `-var-file` on the command line.
4. Environment variables `TF_VAR_name`.

Types: `string`, `number`, `bool`, collections (`list`, `map`, `set`), and composites (`object`, `tuple`). Use `validation` blocks on `variable` to reject absurd values before `plan`.

@section: Outputs and sensitive data

`output` can set `sensitive = true` to reduce exposure in console logs. It is **not** strong encryption: avoid dumping secrets to public pipelines or chat notifications.

When an output feeds another system (e.g. a Helm deploy pipeline), document the contract: type, format, and whether it may be null in test environments.

@section: Day-to-day mistakes

* Forgetting `terraform init` after clone or after adding a provider.
* Uploading `.tfstate` to public repositories (leaks IDs and sometimes internal data).
* Using `apply -auto-approve` in production without reviewing `plan` or without a pinned plan artifact.
* Not pinning provider versions: the same code produces different plans weeks later.
* Mixing naming conventions that the provider normalizes (surprises on import).

@section: When to use `data` vs `resource`

* **`resource`:** Terraform owns the object lifecycle.
* **`data`:** read-only lookup; something else may have created the object.

If you need to **adopt** an existing resource, the supported path is usually `terraform import` (careful: configuration must match reality).

@section: Suggested lab

1. Install Terraform locally or use an official container.
2. Create a directory with `main.tf` defining a minimal resource in a test account (e.g. a uniquely named bucket) parameterized by `variable "project"`.
3. Run `init`, `validate`, `plan` **without** `apply` first and read each section of the plan.
4. Add an `output` and plan again; observe how output changes.

@quiz: Which command must you run after cloning a Terraform repo before `plan`?
@option: terraform apply
@correct: terraform init
@option: terraform refresh only

@quiz: What is `terraform plan` mainly for?
@option: Formatting .tf files
@correct: Showing what Terraform would change without applying until you confirm
@option: Deleting state
