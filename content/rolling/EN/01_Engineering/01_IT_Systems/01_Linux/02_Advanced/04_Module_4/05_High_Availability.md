@title: High Availability: Clusters and Load Balancers
@icon: ⚖️
@description: Design systems that survive failures. VIPs, Keepalived VRRP, HAProxy, and an intro to Pacemaker.
@order: 5

# High availability: VRRP, load balancing, and Pacemaker clusters

Removing **single points of failure** needs redundant **hardware**, **network**, and **data**. This lesson combines **Keepalived (VRRP)** for floating IPs with **HAProxy/Nginx** as load balancers and an overview of **Pacemaker/Corosync** for clustered services.

@section: 1. VIP and VRRP

**VRRP** lets several nodes share a **virtual IP**; only the **master** answers. If it dies, the **backup** takes over in seconds.

**Keepalived** (`/etc/keepalived/keepalived.conf`):

* `state MASTER` / `BACKUP`
* `priority` (higher = preferred)
* unique `virtual_router_id` on the LAN
* `virtual_ipaddress { ... }`

**Health checks:** `vrrp_script` lowers priority if a local service fails.

@section: 2. Load balancing (L4 and L7)

**HAProxy** (TCP L4 / HTTP L7):

* Algorithms: `roundrobin`, `leastconn`, `source` (sticky).
* **Health checks:** `option httpchk`, `check` on backends.

**Nginx/HAProxy** in front of multiple **upstreams**.

**Active-active** — all nodes receive traffic; **active-passive** with VIP for legacy patterns.

**Minimal HAProxy example:** a `frontend` listens on `:443`, a `backend` lists three servers with `check inter 2s fall 3 rise 2`. After two failed checks the node is marked *down* and traffic goes elsewhere. A **stats socket** (`stats socket /run/haproxy/admin.sock`) lets you inspect state live without restarting.

**TLS passthrough vs termination:** balance raw TCP (443 → backends 443) or terminate TLS on HAProxy (certs on the LB). The first keeps encryption to the app; the second centralizes certs and crypto CPU.

@section: 3. Pacemaker and Corosync

For **databases** or resources that are not trivially duplicated:

* **Quorum** — majority of nodes alive (avoids split-brain).
* **STONITH / fencing** — power off the losing node (IPMI, iDRAC).
* **Resources:** floating IP, `systemd:nginx`, **DRBD** for shared block.

**Complexity:** misconfigured HA clusters cause more outages than they prevent.

**Quorum by the numbers:** with **three nodes** you tolerate one failure and keep a majority (2≥2). With **two nodes**, a network partition causes **split-brain** unless you have **fencing** or a **witness** (third opinion). Serious clusters use an odd node count or external arbitration.

**STONITH in practice:** power off the losing node via **iDRAC/IPMI**, **fence_vmware**, **fence_aws**, etc.—depends where the VM lives. Without fencing, two nodes may both believe they own the resource and corrupt data (especially without cluster-aware filesystems).

@section: 4. DNS as crude load balancing

Multiple A records in **round-robin** — simple but does not remove dead nodes without health checks (use **GSLB** or cloud health-checked endpoints).

@section: 5. Shared data

Application HA usually needs **shared storage** (SAN, NFS with care, replicated databases). **DRBD** mirrors block devices between two nodes.

@section: 6. Observability

Monitor VIP reachability, HTTP endpoints, HAProxy **stats socket**. Alert from Prometheus/Grafana.

@section: 7. RHEL vs Debian

* **RHEL High Availability Add-On** documents Pacemaker + fence agents.
* **Keepalived** and **HAProxy** are packaged everywhere.

@section: 8. Cloud and managed load balancers

On AWS (**ELB/ALB**), GCP (**Cloud Load Balancing**), or Azure, the control plane is the provider’s: health checks, TLS, horizontal scale. **Keepalived/HAProxy** skills still matter for **Kubernetes** (MetalLB, kube-vip) or **on-prem**—same mental model: VIP + health checks + backends.

@section: 9. Extended lab

1. Two VMs with Keepalived sharing a VIP; stop the master and measure failover time (often 1–5 seconds depending on `advert_int` and network).
2. Document **split-brain** risk if the inter-node link fails but both nodes stay up; propose mitigation (fencing, quorum device, or a third node).
3. Configure HAProxy on one VM in front of two static HTTP backends and observe behavior when you stop one (`ss` + logs).
4. In two paragraphs, when would you choose **active-active** vs **active-passive** for a traditional relational database?

@quiz: Which protocol does Keepalived use to move a virtual IP between nodes?
@option: BGP
@correct: VRRP
@option: ARP

@quiz: What prevents two cluster nodes from corrupting the same resource?
@option: DHCP
@correct: Quorum + fencing (STONITH)
@option: cron

@quiz: Which component typically performs HTTP health checks on backends?
@option: bind
@correct: HAProxy (or similar LB) with `check`
@option: tcpdump
