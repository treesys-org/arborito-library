@title: Ansible Roles: Structure and Galaxy
@icon: 🎭
@description: Role layout, defaults vs vars, dependencies, and ansible-galaxy.
@order: 4

# Roles: packaging reuse

A **role** is a folder with a fixed layout (`tasks`, `handlers`, `templates`, `files`, `defaults`, `vars`, `meta`). Roles let you share configuration across playbooks and publish to **Ansible Galaxy** or private repos. This lesson covers layout, contract, dependencies, and testing.

@section: Standard layout

```
roles/nginx/
  tasks/main.yml
  handlers/main.yml
  templates/nginx.conf.j2
  files/static-site/
  defaults/main.yml
  vars/main.yml
  meta/main.yml
```

* **defaults/main.yml:** **lowest precedence** defaults; consumers can override easily.
* **vars/main.yml:** higher precedence inside the role; use for internal constants that should not change without reviewing the role.
* **meta/main.yml:** dependencies on other roles (`dependencies:`), platform compatibility.

@section: Using a role in a play

```yaml
- hosts: web
  roles:
    - role: nginx
      vars:
        worker_processes: 4
```

Modern alternative: `import_role` / `include_role` inside `tasks` when you need fine-grained ordering with other tasks.

@section: Ansible Galaxy and requirements

`ansible-galaxy install geerlingguy.nginx` downloads roles to `~/.ansible/roles` or a configured path. **Pin versions** in `requirements.yml`:

```yaml
roles:
  - name: geerlingguy.nginx
    version: 3.1.0
```

Install in CI with `ansible-galaxy install -r requirements.yml` before the playbook.

@section: Role contract

Document in README:

* Required and optional variables.
* Supported platforms (families, versions).
* Side effects (restarts, enabled services).

Anti-pattern: a giant role with dozens of mandatory variables without sensible defaults; split into composed roles.

@section: Testing with Molecule

**Molecule** tests roles against containers or VMs: converge, idempotence check, verifiers (testinfra, ansible). Integrates **ansible-lint** for style.

Typical flow:

1. `molecule create` — provision test instance.
2. `molecule converge` — apply the role.
3. `molecule idempotence` — second pass with no changes.
4. `molecule verify` — assertions.
5. `molecule destroy` — cleanup.

@section: Organization in monorepos

Some teams keep roles under `roles/` in the application repo; others use dedicated versioned repos. Choose based on change velocity and number of consumers.

@section: Common mistakes

* Copying a Galaxy role without reading the README and then opening tickets to the author for mis-passed variables.
* Putting secrets in `defaults` instead of vault.
* Circular dependencies between roles in `meta/main.yml`.

@section: Suggested lab

1. Run `ansible-galaxy init roles/demo` and inspect the generated structure.
2. Move two tasks from a monolithic playbook into `roles/demo/tasks/main.yml` and call the role from `site.yml`.
3. Add `defaults/main.yml` with a parameter and use it in a template.
4. (Optional) Initialize Molecule in the role and run a local converge.

@quiz: What is the main purpose of `defaults/main.yml` in a role?
@option: Store always-mandatory secrets
@correct: Define lowest-precedence defaults that the consumer can override
@option: List dependencies on other roles
