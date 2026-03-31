@title: Concepts: Forests, Trees, and Domains
@icon: 🌲
@description: Understanding the Active Directory hierarchy.
@order: 1

# Forests, trees, and domains

**AD DS** is the directory service that stores objects and authenticates access. You must know **forest**, **tree**, **domain**, **OU**, and **trust** vocabulary for exams and real jobs.

@section: Domain

A **domain** is a **security and administrative boundary** sharing a DNS name and directory partition.

@section: Tree

A **tree** is a set of domains in a **contiguous DNS namespace** (e.g. `sales.contoso.com` under `contoso.com`).

@section: Forest

A **forest** is the top-level **security boundary** for AD schema and enterprise configuration. Multiple trees can live in one forest with **transitive trusts** between domains in that forest.

@section: Organizational units (OUs)

**OUs** structure objects and are the anchor for **delegation** and **GPO linking**—not the same as built-in containers like `Users`.

@section: Schema and partitions

*   **Schema** defines object attributes (unique per forest).
*   **Configuration** partition stores sites, replication topology.
*   **Domain** partition per domain.

@section: Global Catalog (GC)

GCs hold a partial attribute set for **forest-wide** searches and logon in multi-domain forests.

@section: DNS dependency

Clients locate DCs via **DNS SRV records**. Broken DNS = broken domain join and slow logon.

@quiz: What is the top-level Active Directory security boundary?
@option: OU
@correct: Forest
@option: Site

@quiz: Which service must clients use to locate domain controllers?
@option: DHCP only
@correct: DNS with correct SRV records for the domain
@option: WINS
