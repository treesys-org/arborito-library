@title: Grafana: Dashboards, Datasources, and Alerts
@icon: 📊
@description: Visualization, variables, provisioning, and unified alerting.
@order: 3

# Grafana: visualize and alert on metrics and logs

**Grafana** is an observability platform that queries **datasources** (Prometheus, Loki, Elasticsearch, Tempo, cloud vendors) and renders **dashboards**. It can also **alert** (Unified Alerting) with notification channels. This lesson covers concepts, variables, provisioning, and practices.

@section: Datasources and panels

A **datasource** points to a Prometheus, Loki, etc. URL with authentication. A **dashboard** contains **panels** (time series, tables, heatmaps, stat).

Each panel runs a query (PromQL, LogQL…) with **interval** and **step** you should tune to the time range for performance.

@section: Dashboard variables

**Variables** (`$cluster`, `$namespace`) let one dashboard serve many environments. Sources: Prometheus query, custom, datasource.

**Cascading variables** reduce cardinality in the UI and avoid copy-paste dashboards per environment.

@section: Provisioning

In GitOps, version dashboards as JSON/YAML under `provisioning/dashboards` or use **Grafana Operator** on Kubernetes. Dashboards become **code** reviewed in PRs.

**Stable UIDs** avoid duplicates on import.

@section: Alerting in Grafana

**Unified Alerting** defines rules on panel queries or standalone alerts. Integrates with **Alertmanager** or direct channels.

Advantage: one UI for metrics and logs if you use Loki. Risk: duplicating rules already in Prometheus; **document** the source of truth for each alert.

@section: Organization and permissions

* **Folders** by team or domain.
* **Roles** (viewer, editor, admin) via SSO (OAuth, SAML).

In multi-tenant environments, separate instances or organizations if isolation requires it.

@section: Dashboard best practices

* Panels with **titles and descriptions** explaining what to do if the curve breaks.
* Correct **units** (s, ms, percent, bytes).
* Avoid dozens of panels in one dashboard; split by troubleshooting story.

@section: Plugins

Official and community plugins extend datasources and visualizations. **Pin** versions in production deployments and review licenses.

@section: Common mistakes

* Queries scanning years of data with 1s step (browser and backend overload).
* Dashboards without variables multiplying copies for “prod”, “staging”, …
* Datasource credentials only in the UI without backup or audit.

@section: Suggested lab

1. Install Grafana locally or use the official image.
2. Add Prometheus as a datasource and create a panel with `up`.
3. Create a `job` variable populated from Prometheus and use it in a query.
4. Export the dashboard JSON to a test repo.

@quiz: What are dashboard variables mainly for?
@option: Removing the need for datasources
@correct: Parameterizing queries and reusing one dashboard across environments or services
@option: Only toggling light/dark theme
