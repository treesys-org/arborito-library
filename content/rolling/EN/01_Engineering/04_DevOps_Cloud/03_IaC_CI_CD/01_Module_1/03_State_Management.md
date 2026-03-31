@title: Remote State and Locking
@icon: 🔐
@description: S3 backends, locking, workspaces, and team practices.
@order: 3

# Remote state, locking, and teams

The `terraform.tfstate` file is the **index** of what Terraform believes it created: real IDs in the provider, dependencies, and sometimes derived data. Without coherent state, plans are unpredictable or destructive. For teams, **local-only** state does not scale: you need a **remote backend**, **encryption**, **locking**, and **permissions** so two people or pipelines do not corrupt the same deployment.

@section: Why Git alone is not the backend (as the only solution)

Git versions **code** extremely well. State, however:

* Changes on every `apply` with IDs and attributes you do not want to merge like application code.
* May contain sensitive values if someone pushed secrets into resources carelessly.
* Must be the **current** view Terraform uses for the next plan, not a merge history.

The usual practice is **remote backend** with encryption at rest and role-based access; committing `.tfstate` to the application repo is the exception, not the rule.

@section: S3 + DynamoDB pattern (common on AWS)

Typical configuration:

```hcl
terraform {
  backend "s3" {
    bucket         = "my-org-tfstate"
    key            = "project/vpc/terraform.tfstate"
    region         = "eu-west-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

* **S3** stores the state object; enable object versioning so you can restore if something goes wrong.
* **DynamoDB** provides **locking**: if an `apply` is running, another process gets a lock error until it finishes or the lock is released safely.

The bucket and table are often created in a separate **bootstrap** stack or management account, with policies preventing accidental deletion.

@section: Other backends

* **Terraform Cloud / Enterprise:** managed state, runs, workspace permissions.
* **Azure Storage** with blob lease, **GCS** with versioned buckets.
* **Consul**, **etcd** in specialized environments.

Choose based on compliance (data residency), cost, and team maturity on that platform.

@section: Terraform workspaces

`terraform workspace` allows **multiple states** under the same code with prefixes in the backend (`env:/dev/`, `env:/prod/`). Advantage: one directory. Risk: confusing the active workspace and applying to the wrong environment.

A very common alternative: **per-environment directories** (`envs/dev`, `envs/prod`) with distinct backends and explicit variables; more verbose but harder to mistake if CI is clear.

@section: Migrating local state to remote

Safe procedure:

1. Agree a window where nobody else runs `apply` on that stack.
2. Create the remote backend and IAM/role permissions for CI and authorized humans.
3. Add the `backend` block and run `terraform init -migrate-state`; Terraform will ask for confirmation.
4. Verify the remote object exists.
5. Run `terraform plan` and confirm it does **not** propose mass unexpected recreation (if it does, fix resource names and providers before continuing).

@section: Rotation, backup, and recovery

* Enable **versioning** of state objects in S3 or equivalent.
* Before large changes (module refactors, mass imports), export a copy or rely on previous object versions.
* **Corrupt or stuck state:** the official docs describe `terraform state` subcommands and `import`; use `force-unlock` only when you are sure no real apply is running.

@section: Security and operations practices

* Least-privilege IAM: the CI role only needs state bucket access plus resources for that project (ideally in a dedicated account).
* Separate cloud **accounts** per environment when the organization allows it; reduces blast radius.
* Tag resources with `Environment`, `Owner`, `TerraformWorkspace` or equivalents for audit and cost.

@section: Awkward situations (and what to do)

* **Two teams touching the same stack:** unify ownership or split code into stacks with `terraform_remote_state` and clear output contracts.
* **Drift on plan:** decide whether to import reality into code, revert console changes, or accept Terraform’s proposed change — never blindly in production.

@section: Suggested lab

1. Create a private S3 bucket with encryption and versioning, and a DynamoDB table for locks (follow current Terraform docs for the `s3` backend).
2. Configure a minimal project with remote backend and run `init`, `plan`, and a test `apply` in a sandbox account.
3. Launch two concurrent `apply` processes from two terminals and observe locking behavior; release the lock correctly when done.

@quiz: What is the DynamoDB table for in the classic S3 Terraform backend?
@option: Storing HCL code
@correct: Providing locking to prevent concurrent applies on the same state
@option: Storing mandatory audit logs

@quiz: What is the main risk of putting `terraform.tfstate` in a public Git repo?
@option: Terraform runs slower
@correct: Exposure of resource IDs and possible leakage of sensitive deployment data
@option: Git rejects .tf files
