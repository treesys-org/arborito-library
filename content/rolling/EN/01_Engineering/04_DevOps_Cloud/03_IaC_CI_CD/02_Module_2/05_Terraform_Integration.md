@title: Terraform + Ansible Integration: Splitting Responsibilities
@icon: 🔗
@description: Terraform for cloud, Ansible for the OS; remote-exec and provisioners.
@order: 5

# Terraform and Ansible together: who does what

Many teams use **Terraform** to create infrastructure (VPCs, subnets, instances, load balancers, security groups) and **Ansible** to configure the operating system (packages, users, services, files). This separation is healthy: each tool shines in its domain and reduces coupling between the cloud layer and the machine layer. This lesson also explains why Terraform **provisioners** are usually a poor choice for complex configuration.

@section: Recommended pattern

1. `terraform apply` creates instances with **tags** or **metadata** Ansible will use as dynamic inventory criteria.
2. The pipeline (or manual job) runs Ansible against a filtered inventory (e.g. `Role=web` and `Env=prod`).

**Avoid** Terraform `remote-exec` to install long-running applications: it is hard to debug, not idempotent by default, and mixes responsibilities.

@section: Dynamic inventory from the cloud

Inventory plugins for AWS (`aws_ec2`), Azure, GCP automatically generate groups from tags. Terraform tags resources; Ansible discovers them without manual lists.

Conceptual example: Terraform assigns `tags = { Role = "app", Env = "staging" }`; Ansible limits with `-l` or groups derived from the plugin.

@section: Execution order and dependencies

Ansible needs **connectivity**: SSH open, bootstrap user, keys or SSM. If Terraform just created the VM, **cloud-init** may still be running. Use:

* Ansible `wait_for` on port 22.
* Provider health checks before marking the apply successful in pipelines.

**Terraform outputs:** export IPs or DNS in `output` and pass them to the playbook with `-e` or generate an intermediate inventory. **terraform_remote_state** in another stack can feed Ansible variables if the flow is documented.

@section: Alternative: minimal cloud-init

`user_data` can install Python, a deploy user, and SSH keys; put the rest in Ansible. Keep cloud-init **minimal** to avoid duplicating logic in two places.

@section: Terraform-only

* **Immutable images** (Packer) with no post-boot SSH configuration.
* Fully managed services (RDS, Lambda) with no OS to tune.

@section: Ansible-only

* Infrastructure already exists or is owned by another team without Terraform.
* On-prem without a unified cloud API.

@section: Anti-pattern: Terraform provisioners

`remote-exec` and `local-exec` provisioners in Terraform:

* Run at specific lifecycle points and can fail opaquely.
* Do not replace Ansible’s idempotent model.
* Complicate `taint` and reprocessing.

Reserve `local-exec` for **short hooks** (notify an external system) if you truly need it.

@section: Security

* Do not embed SSH keys in Terraform in plaintext; use provider mechanisms (metadata, IAM roles for SSM, etc.).
* Limit what the Ansible pipeline can run in production (inventory, tags, approvals).

@section: Suggested lab

1. Create a VM with Terraform and tag it `App=demo` and `Env=dev`.
2. Configure `ansible-inventory` with your cloud plugin and verify the VM appears in the expected group.
3. Run `ansible -m ping` against that group.
4. Document in a one-paragraph README the order: apply → wait → ansible.

@quiz: Why is Terraform’s `remote-exec` provisioner discouraged for complex configuration?
@option: Terraform does not support SSH
@correct: It mixes infrastructure provisioning with OS configuration, is hard to debug, and is hard to repeat idempotently
@option: Ansible cannot use dynamic inventory
