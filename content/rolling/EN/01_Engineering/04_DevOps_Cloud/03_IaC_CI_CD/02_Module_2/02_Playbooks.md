@title: Playbooks and Tasks: YAML, Handlers, and Conditions
@icon: 📋
@description: Plays, tasks, handlers, when, loops, and readability practices.
@order: 2

# Playbooks: from readable YAML to production

A **playbook** is YAML describing **what** to do on **which hosts**. Readability matters: during incidents you will read this under stress; clear task names and consistent structure reduce errors. This lesson covers plays, tasks, handlers, conditions, loops, and common pitfalls.

@section: Anatomy of a play

```yaml
- name: Configure web tier
  hosts: web
  become: true
  vars:
    app_version: "2.3.1"
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true
```

Each **task** invokes a **module** with arguments. The `name` field is **documentation**; describe business actions (“Install nginx”), not raw module names.

**Serialization:** `serial: "25%"` or a fixed number for rolling updates (e.g. cluster restarts).

@section: Handlers

**Handlers** are special tasks that run **at the end of the play** if they were **notified** by a change (`notify`). Typical pattern: restart a service only if configuration changed.

```yaml
- name: Render nginx.conf
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart nginx

handlers:
  - name: Restart nginx
    ansible.builtin.service:
      name: nginx
      state: restarted
```

**Note:** if several tasks notify the same handler, it runs **once** at the end (unless `force_handlers` or intermediate errors). If you need strict ordering between handlers, use a chain of handlers or a normal task with `when`.

@section: Conditions and expressions

* `when: ansible_os_family == "Debian"` — branches by OS or facts.
* `failed_when` — redefine when a task counts as failed (useful with ambiguous `command` exit codes).
* `changed_when` — adjust `changed` semantics for scripts that always print something.

Combine with `ansible_facts` gathered at play start (`gather_facts: true` by default).

@section: Loops and lists

* `loop:` with a list or dict.
* `loop_control:` for `label` and concurrency limits in some contexts.

Prefer `loop` over legacy `with_items` in new playbooks (check your Ansible version docs).

@section: Jinja2 in templates

Host variables (`hostvars`), group variables (`group_vars`), vault, and play `vars` inject into `.j2` templates. Keep Jinja logic **simple**; if it grows, use `set_fact` sparingly or dedicated filters.

**Diffs:** `ansible-playbook --diff` shows file changes before applying in sensitive environments.

@section: Ansible Vault

Secrets in Git should be encrypted with `ansible-vault encrypt` or `encrypt_string`. In CI, inject the password or key file via a secure environment variable or pipeline mechanism. **Never** paste plaintext secrets into public repositories.

@section: Roles and import_tasks vs include_tasks

`import_tasks` resolves at playbook load time; `include_tasks` is dynamic at runtime. For most organization, **roles** are the canonical structure (see the roles lesson).

@section: Common mistakes

* Incorrect YAML indentation (spaces, not tabs).
* Forgetting `become` on tasks that need root.
* Handlers not firing because the task did not report `changed` (check modules and `changed_when`).
* `shell` with pipes without quoting and escaping surprises.

@section: Suggested lab

1. Write a playbook that installs `nginx` and deploys `index.html` with `template`.
2. Add an nginx restart handler and verify it only runs when the template changes (run twice in a row).
3. Add a conditional `when` task that installs a package only on Debian/Ubuntu.

@quiz: When do Ansible handlers normally run?
@option: Before each task
@correct: At the end of the play, if notified by a task that changed the system
@option: Never — they are documentation only
