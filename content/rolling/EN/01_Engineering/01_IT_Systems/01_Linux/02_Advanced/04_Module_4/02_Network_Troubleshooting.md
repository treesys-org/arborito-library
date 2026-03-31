@title: Network Troubleshooting: tcpdump and CLI analysis
@icon: 🔬
@description: When ping is not enough. Capture packets, inspect sockets, and find why connections fail.
@order: 2

# Advanced network diagnostics: tcpdump, ss, mtr, traces

When “the network works” but the service does not, you need **evidence**: packet captures, socket state, routes. This extends **ping/traceroute** with **LPIC-2** and production-grade tools.

@section: 1. tcpdump

```bash
sudo tcpdump -i eth0 -n host 192.168.1.50 and port 443
sudo tcpdump -i any -w capture.pcap -c 1000
```

* `-n` — no reverse DNS on the fly.
* `-w` — save for **Wireshark**.
* BPF filters: `tcp`, `udp`, `host`, `port`, `net`.

**TCP handshake:** SYN → SYN-ACK → ACK. Only SYN retransmits with no SYN-ACK → **no response** (firewall DROP, bad route, dead host). **RST** → explicit reject.

@section: 2. ss (replaces netstat)

```bash
ss -tlnp        # listening TCP with process
ss -tanp        # all TCP with timers
ss -s           # summary
```

**`ss`** uses **netlink** and is faster than legacy `netstat`.

@section: 3. mtr

Combines ping and traceroute with per-hop stats:

```bash
mtr -rwzbc 100 example.com
```

Useful for **intermittent loss** on WAN paths.

@section: 4. DNS: dig

```bash
dig +trace example.com
dig @8.8.8.8 A api.example.com
```

If the app fails but `curl` to an IP works, suspect **DNS** (systemd-resolved, `/etc/resolv.conf`, search domains).

@section: 5. Application-layer checks

```bash
curl -vI https://server
openssl s_client -connect host:443 -servername host
nc -vz host 5432
```

**TLS:** incomplete chain, wrong SNI, expired cert — `openssl s_client` shows it.

@section: 5b. MTU, black holes, and Path MTU Discovery

Classic symptom: **SSH hangs after login**, **HTTPS loads halfway**, **SMB works with small packets**. A **VPN** tunnel or misconfigured router may mishandle fragmentation or block **ICMP Fragmentation Needed**. Try:

```bash
ping -M do -s 1400 example.com   # do not fragment; lower -s until it passes
tracepath -n example.com
```

Adjusting **MTU** on the interface or VPN client often fixes it. `tcpdump` shows retransmits without ACK when the path is the problem.

@section: 5c. conntrack and nftables

On stateful firewalls, exhausting **conntrack** (`nf_conntrack: table full`) causes silent drops. Check:

```bash
cat /proc/sys/net/netfilter/nf_conntrack_count
sudo conntrack -L | wc -l   # if installed
```

With **nftables**: `sudo nft list ruleset` and counters; with legacy **iptables**: `iptables-save | less`.

@section: 6. Namespaces and firewalls

If **iptables**/**nftables** or **firewalld** block, tcpdump may still see traffic on an interface **before** filtering; distinguish **INPUT/OUTPUT** vs **FORWARD**.

@section: 7. Forensic workflow

1. Reproduce with a known client.
2. In parallel: **`ss`** on server, **tcpdump** on client or server.
3. Save **pcap** for the ticket; in Wireshark filter **`tcp.stream`**.

@section: 8. Lab

1. Capture TLS to a public site (`tcpdump port 443`) and inspect the handshake in Wireshark.
2. Compare `ss -tn` before and after starting a web service.
3. Force a low MTU on a VM (`ip link set dev eth0 mtu 1200`) and observe large downloads vs small `curl`.
4. With `nft` or `iptables`, count DROP rules and relate to retransmits in `tcpdump`.

@quiz: Which modern tool replaces netstat for listing sockets with owning processes?
@option: ifconfig
@correct: ss
@option: route

@quiz: Which tcpdump pattern suggests a firewall silently dropping packets?
@option: SYN then immediate RST
@correct: Repeated SYN with no SYN-ACK
@option: ICMP echo reply

@quiz: What is `openssl s_client` used for in troubleshooting?
@option: Key generation only
@correct: Live TLS handshakes and certificate inspection
@option: Firewall configuration
