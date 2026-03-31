@title: Network Automation: APIs, IaC, and GitOps
@icon: 🤖
@description: NETCONF/RESTCONF, Ansible, Jinja templates, change testing.
@order: 5

# Network automation: less hand-CLI, more review

**Automation** reduces human error and speeds auditable changes: **APIs** (`NETCONF/RESTCONF`, vendor REST), **Ansible**, **Terraform** (cloud networking), **GitOps** for policy-as-code. This lesson lists **patterns** and **risks** (credentials, drift).

@section: Modern APIs

**NETCONF** over SSH with **XML/YANG** data; **RESTCONF** HTTP-friendly. Advantage: structured models vs CLI scraping.

**gNMI** streams telemetry to **Prometheus/Grafana**.

@section: Ansible for networking

`ios`, `nxos`, `junos` modules apply idempotent config with **Jinja2** templates per role (core/distribution/access).

**Dynamic inventory** from CMDB or NetBox.

@section: Infrastructure as code

For **VPC/VPN** in cloud, Terraform + pipelines. **Drift detection** on a schedule.

@section: Testing

**Batfish**, **pyATS** validate changes before production; **canary** on pilot devices.

@section: Security

**RBAC** on controllers, **vault** for credentials, **audit logs** for changes.

@section: Common mistakes

* Automation without a known **rollback**.
* Mixed sources of truth (manual CLI + Git).

@section: Suggested lab

1. Run a read-only Ansible **gather_facts** playbook against IOS-XE in GNS3/EVE-NG.
2. Version Jinja templates in Git with mandatory PRs.
3. Design a pipeline: lint → simulation → change window.

@quiz: What main advantage does NETCONF/YANG offer over expect-based CLI scripts?
@option: Always faster at runtime
@correct: Structured models and more predictable transactions than text scraping
@option: Eliminates the need for routing
