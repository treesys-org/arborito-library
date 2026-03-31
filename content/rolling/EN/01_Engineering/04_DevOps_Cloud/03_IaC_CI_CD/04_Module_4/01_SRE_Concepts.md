@title: SRE Concepts: SLOs, Error Budget, and Toil
@icon: 🛡️
@description: Reliability as engineering, indicators, error budgets, and reducing manual work.
@order: 1

# Site Reliability Engineering: measurable reliability

**SRE (Site Reliability Engineering)** is a discipline that applies software engineering principles to **operations**: automate, measure, agree service levels, and decide when to stop shipping features if reliability is at risk. This lesson introduces **SLIs**, **SLOs**, **SLAs**, **error budgets**, and **toil**, without confusing them with marketing.

@section: SLIs, SLOs, and SLAs

* **SLI (Service Level Indicator):** a measurable metric (p99 latency, availability, error rate).
* **SLO (Service Level Objective):** an **internal target** on an SLI (e.g. “99.9% of requests < 300 ms over 30 days”).
* **SLA (Service Level Agreement):** a **contract** with customers or the business; breach often has financial or reputational consequences.

SLOs should be **stricter** than SLAs to leave margin. If you promise too much in an SLA without measuring well, you pay the price.

@section: Error budget

The **error budget** is “how much you can fail” and still meet the SLO. Example: 99.9% monthly availability allows ~43 minutes of downtime.

If the budget is exhausted:

* **Freeze** non-essential launches.
* Prioritize **stability** work and operational debt reduction.

If budget is consistently under-spent, you can **accelerate** delivery or renegotiate the SLO if infrastructure cost does not justify the target.

@section: Toil

**Toil** is manual, repetitive work that scales linearly with the service (repetitive tickets, console clicking). SRE aims to **automate** or eliminate toil to free time for reliability projects (autoscaling, chaos, observability improvements).

Not all one-off manual work is toil: a unique migration can be acceptable; the problem is the recurring pattern.

@section: Blameless postmortems

After incidents, a **postmortem** documents:

* Timeline and impact.
* Root cause (often multiple factors).
* Corrective actions with owners.

**Blameless** culture focuses on systems and processes, not individuals, so information flows.

@section: Capacity and reliability

**N+1** redundancy, **autoscaling**, **resource limits**, and load tests are part of SRE work. Reliability is not only “more replicas”: it is understanding **bottlenecks** (databases, queues, external dependencies).

@section: Relationship to DevOps

**DevOps** is broad culture and practices; **SRE** is a concrete role/framework with strong emphasis on **measurement** and **error budgets**. Many teams adopt SRE practices without the job title “SRE.”

@section: Common mistakes

* Trusting “99.99%” without defining how the SLI is measured.
* Ignoring the error budget until a major incident.
* Measuring load balancer uptime but not user-observed experience (synthetic monitoring).

@section: Suggested lab

1. Pick a service you use and define **one** measurable SLI (latency or success rate).
2. Propose a realistic monthly SLO and compute the error budget in minutes or failed requests.
3. List three sources of toil on your team and one automation idea for each.

@quiz: What does the error budget represent in SRE practice?
@option: The quarterly cloud budget
@correct: Acceptable failure or downtime without violating the SLO
@option: The maximum number of deployments per day
