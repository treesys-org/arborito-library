@title: QoS: Queues, Marking, and Congestion
@icon: 📶
@description: DSCP/CoS, shaping vs policing, LLQ, WAN QoS.
@order: 3

# QoS: when bandwidth is not enough

**QoS** manages **congestion** by marking traffic (**DSCP**, **802.1p CoS**), assigning **queues** (CBWFQ, **LLQ** for voice), and applying **policing/shaping**. This lesson avoids myths: QoS **does not create** bandwidth; it **prioritizes** and limits.

@section: Marking

* **Layer 2:** `802.1p` in VLAN tags (3 bits).
* **Layer 3:** **DSCP** in the IP ToS field (EF, AFxy classes).

**Trust boundary** at the access switch: trust phone marks or rewrite.

@section: Congestion and queues

**Tail drop** is the brutal default. **WRED** reduces TCP synchronization. **LLQ** strictly prioritizes voice with an integrated policer so it cannot starve everything else.

@section: Policing vs shaping

* **Policing:** drops or remarks excess (edge).
* **Shaping:** buffers to conform to a contracted WAN rate.

@section: WAN

**LLQ + CBWFQ** on branch routers; align with **provider QoS** classes (MPLS) if you have an agreed **mapping**.

@section: Common mistakes

* Marking everything **EF** → real voice suffers.
* **DSCP** not preserved end-to-end (marks lost on a hop).

@section: Suggested lab

1. Mark ICMP vs UDP with distinct **DSCP** in Linux (`tc`).
2. Apply `HTB`/`HFSC` in a lab and measure latency under load.
3. Document a class map for a fictional WAN.

@quiz: What does LLQ best describe in QoS policies?
@option: A non-priority queue
@correct: A low-latency/strict-priority queue for sensitive traffic (e.g. voice) with a cap to avoid starvation
@option: Packet encryption
