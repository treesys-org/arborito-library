@title: Sites and AD Replication Design
@icon: 🔁
@description: Optimizing replication across distributed networks.
@order: 5

# Sites and replication

**Sites** map **IP subnets** to physical locations. The **KCC** builds a replication topology; **site links** define cost and schedule across WAN links.

@section: Why sites matter

Without correct sites, clients may authenticate to **distant DCs** and replication may **saturate WAN** links.

@section: Key objects

*   **Site** → contains DCs and subnet associations.
*   **Site link** → connects sites (cost + replication interval).
*   **Bridgehead servers** → chosen DCs for inter-site replication.

@section: Tools

*   `dssite.msc`
*   `repadmin /replsummary`

@section: Lab idea

Create two sites, associate subnets, move a test DC, and observe **Directory Service** events.

@quiz: What object defines cost and schedule between AD sites?
@option: OU
@correct: Site link
@option: GPO link

@quiz: Which component builds the replication topology between domain controllers?
@option: DHCP
@correct: KCC (Knowledge Consistency Checker)
@option: DNS scavenging
