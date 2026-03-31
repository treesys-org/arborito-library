@title: Ansible Architecture: Inventory, Playbooks, and Connection
@icon: 🎭
@description: Inventory, plugins, execution strategy, and differences from permanent agents.
@order: 1

# Ansible: architecture without a persistent agent

Ansible automates configuration, application deployment, and orchestration **without installing a persistent agent** on managed nodes: it uses SSH on Linux and WinRM on Windows. The **control node** (your laptop, a bastion, or a CI runner) runs `ansible-playbook` and pushes Python or PowerShell modules over the connection. This lesson establishes the mental model: inventory, transport, privileges, and limits of the model.

@section: Main components

* **Inventory:** list of hosts and groups (`inventory.ini`, static YAML, or dynamic cloud sources).
* **Playbook:** ordered list of **plays**; each play applies **tasks** to a host pattern (`hosts:`).
* **Modules:** idempotent units (`apt`, `copy`, `systemd`, `k8s`, `win_regedit`, …).
* **Plugins:** connection (SSH, paramiko), inventory (AWS EC2, Azure RM), callback (output formatting), Jinja2 filters, lookups (vault, files).

**Execution:** by default order is **linear** per host (task 1 on all hosts, then task 2…), configurable with strategies.

@section: Static inventory

Minimal INI example:

```ini
[web]
web1.example.com ansible_user=deploy
web2.example.com ansible_user=deploy

[db]
db1.example.com

[web:vars]
http_port=80
```

YAML equivalent: host lists with `hosts:` and `children` for hierarchy. Groups let you apply common variables and limit runs with `-l web`.

@section: Dynamic inventory

Inventory plugins query APIs (EC2, GCP, Azure) and generate hosts and variables on the fly. Useful when fleets scale with autoscaling or tags change constantly. Configure the plugin in `ansible.cfg` or pass `-i` pointing to a script/plugin.

**Good practices:** tag instances consistently (`Role=app`, `Env=prod`); limit inventory with filters so you don’t run against “the whole account.”

@section: Connection and privileges

* **Linux:** SSH with key or password; `ansible_user`, `ansible_ssh_private_key_file`, `ansible_port` per host or group.
* **Elevation:** `become: true` + `become_method sudo` for tasks that need root; `become_user` to impersonate another user.
* **Windows:** `ansible_connection: winrm`, secure credentials, TLS certificates; many enterprises use domains and Kerberos.

**Timeouts:** `timeout` in `ansible.cfg` or per task for slow networks; `async`/`poll` for long operations.

@section: Execution strategy

* **linear:** default; synchronizes progress per task (easier debugging).
* **free:** each host advances as fast as possible (may change relative order among hosts).
* **mitogen** (external) and other optimizations for thousands of nodes.

Choose based on consistency vs speed; for critical patches, **linear** is often easier to reason about.

@section: Idempotence in Ansible

Modules should be **idempotent**: if state already matches, they report `ok` instead of `changed`. If you see `changed` every run, inspect conditions, `command`/`shell` without `creates`, or modules that don’t handle current state well.

**Raw modules:** `command` and `shell` are powerful but dangerous; wrap in scripts with checks or use dedicated modules when available.

@section: When Ansible fits best

* Servers with persistent SSH and medium lifecycle.
* Heterogeneous configuration (Linux + Windows + appliances with APIs).
* Rolling update patterns with `serial:` in the play.

**When it is awkward:** ephemeral containers without SSH (prefer immutable images); networks where only proprietary hardware APIs exist (sometimes Terraform + API + scripts).

@section: Basic security

* **Ansible Vault** for secrets in the repo (encrypted).
* Limit who can run playbooks against production (AWX/Tower, roles, approvals).
* Do not leave private keys in the application repo; use vault or integrated secret managers.

@section: Suggested lab

1. Install Ansible on a control node (OS package or `pip` in a venv).
2. Create two VMs or containers with SSH and a user with sudo.
3. Define an `inventory` with both hosts and run `ansible -m ping all`.
4. Write a one-task playbook that installs an idempotent package (`apt` or `dnf`) and run `ansible-playbook -i inventory site.yml`.

@section: Common mistakes

* Inventory pointing at `localhost` by mistake and “working” only in dev.
* Missing `become` on tasks that need privileges.
* Mixing Python 2/3 on the control node without `ansible_python_interpreter` on exotic hosts.

@quiz: Which statement best describes Ansible compared to permanent agents?
@option: It requires a daemon on every server
@correct: It relies on SSH/WinRM and does not need a persistent agent on managed nodes
@option: It only works on Windows
