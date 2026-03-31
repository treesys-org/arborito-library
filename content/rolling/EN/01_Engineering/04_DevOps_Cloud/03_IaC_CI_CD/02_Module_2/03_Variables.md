@title: Ansible Variables: Precedence and group_vars
@icon: 🔤
@description: host_vars, group_vars, extra-vars, facts, and vault.
@order: 3

# Variables in Ansible: the hierarchy that avoids surprises

Ansible merges variables from many sources: inventory, `group_vars`, `host_vars`, `vars` in the play, roles, `defaults`, `include_vars`, `set_fact`, and the command line. If you do not understand **precedence**, you will spend hours debugging “wrong” values in a template.

@section: Precedence order (simplified)

The official Ansible docs list many levels; conceptually, from lower to higher typical priority:

1. `defaults` inside roles.
2. Inventory variables (`host_vars`/`group_vars` files).
3. `vars` defined in `play` or `include_vars`.
4. Parameters when invoking roles (`roles:` with `vars:`).
5. **`--extra-vars` (`-e`)** on the command line — highest usual override.

**Rule:** use `-e` for punctual overrides in CI or tests; not for secrets in plaintext in public logs.

@section: Typical directory layout

```
inventory/
  production/
    hosts.yml
group_vars/
  all.yml
  web.yml
host_vars/
  web1.yml
```

`group_vars/all.yml` applies to everyone; `group_vars/web.yml` only to group `web`. `host_vars/web1.yml` wins over groups when appropriate according to merge rules.

@section: Facts

Ansible collects **facts** about the system (`ansible_facts`): IPs, distribution, mounts, interfaces. You can cache facts with **fact caching** (Redis, JSON file) to speed large inventories.

**gather_facts:** has a cost; disable (`gather_facts: false`) only if you are sure facts are unnecessary for play logic.

**Custom fact patterns:** some teams use `set_fact` for derived values; remember `set_fact` can persist depending on context.

@section: Vault and secrets

Sensitive variables in `group_vars/secrets.yml` encrypted with vault. In CI, inject the key via environment variable (`ANSIBLE_VAULT_PASSWORD_FILE`) or a securely mounted file.

**Rotation:** rotate secrets in the vault when personnel change or after incidents; do not reuse one vault password for all environments if risk requires separation.

@section: Debugging

* `ansible -m debug -a "var=hostvars[inventory_hostname]" -l web1`
* `ansible-playbook site.yml --diff --check` — simulation where modules support check mode (not all modules do).
* Increase verbosity with `-v` through `-vvv` for connection and variable resolution details (watch for sensitive data in logs).

@section: Variable merging and hash_behaviour

For dictionaries, `hash_behaviour` in `ansible.cfg` controls replace vs merge. Misconfiguration can silently drop partial keys.

@section: Dynamic inventory and variables

Cloud plugins inject per-host variables (tags, zones). **Document** which names your playbook expects so you don’t depend on plugin implementation details.

@section: Common mistakes

* `group_vars` with a group name that does not match inventory (variables “silently” missing).
* Relying on `hostvars` for a host not in the same play without `delegate_to` or `serial` considerations.
* YAML values interpreted as booleans (`yes`/`no`) on older versions.

@section: Suggested lab

1. Define `http_port` in `group_vars/web.yml` and use it in an nginx template.
2. Override with `-e http_port=8080` and observe the result.
3. Encrypt a file with `ansible-vault` and run the playbook with `--ask-vault-pass`.

@quiz: Which mechanism usually has the highest precedence among common ones?
@option: defaults in a role
@correct: Extra variables passed with `-e` / `--extra-vars`
@option: group_vars always wins
