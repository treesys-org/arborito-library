@title: Basic Networking: The Art of Being Connected (ip, ping, dns)
@icon: 📶
@description: The definitive guide (+500 lines) to understanding how your Linux talks to the world. Demystifying IPs, Interfaces, DNS, Gateways, and fixing internet.
@order: 2

# Art of Connection: Network Anatomy Linux

Welcome digital plumbing.

Computer no internet calculator. Network everything. Servers, cloud, DB, games... nothing without net.

How works?
Open browser `google.com`, miracles milliseconds. Fails, user says "Wifi down" reboots router.
Linux user **no blind reboot**. Interrogates system, finds fault, fixes surgical.

Windows network hidden menus, panels, wizards.
Linux network **transparent**. Control packet in out.

Massive guide, dissect stack. No boring OSI theory (Cisco exam). **Survival trench**. Learn identity (IP), shout listen (Ping), phonebook (DNS), exit door (Gateway).

Prepare. Connect cables.

@section: 1. Identity: Who am I? (`ip addr`)

Need identity. Real world ID address. Net **MAC Address** **IP Address**.

Old command `ifconfig`. Tutorial uses `ifconfig`, old (deprecated years).
Standard modern pro **`ip`**.

### 1.1 Listing Interfaces
Write:

```bash
$ ip addr
```
*(`ip a` lazy).*

Output scary. Decipher line line. Two blocks:

**Block 1: Loopback (`lo`)**
```text
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
    inet 127.0.0.1/8 scope host lo
```
*   **What?** Virtual card. Fake.
*   **Why?** Computer talk self.
*   **IP 127.0.0.1:** Sacred. **"Myself"** **"localhost"**. `ping 127.0.0.1` fail, OS broken brain.
*   **Imp:** Internal services (DB) use talk web server same machine, no physical net.

**Block 2: Real Interface (`eth0`, `enp3s0`, `wlan0`)**
```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    link/ether 00:1a:2b:3c:4d:5e brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.45/24 brd 192.168.1.255 scope global dynamic eth0
```
Meat.

1.  **Name:**
    *   **`eth0`:** Classic Ethernet (Cable).
    *   **`wlan0`:** Classic WiFi.
    *   **`enp3s0` / `wlp2s0`:** Modern (Predictable Network Interface Names). Ugly, indicate physical slot. Get used.

2.  **State (`UP` vs `DOWN`):**
    Brackets `<...>`. **`UP`**, on. `DOWN`, off.
    *   *Turn on?* `sudo ip link set eth0 up`.

3.  **Physical Address (`link/ether`):**
    `00:1a:2b:3c:4d:5e`
    **MAC Address**. Serial number burned chip manufacturer. Unique world. Fingerprint hardware.

4.  **Logical Address (`inet`):**
    `192.168.1.45/24`
    **IP Address (IPv4)**. Postal address. Router assigned (DHCP) or manual.
    *   No `inet` line, **no IP**. Disconnected logic world, cable plugged.

### 1.2 Understanding IP Mask (/24)
`/24` end IP. CIDR notation.
Simple:
*   IP 4 numbers: `A.B.C.D`.
*   `/24` means first 3 (`A.B.C`) **Street Name** (Network) last (`D`) **House Number**.
*   IP `192.168.1.45/24`:
    *   Live street `192.168.1`.
    *   Neighbor `45`.
    *   Talk direct neighbors `1` to `254` same street. Talk `192.168.50`, need Router.

@section: 2. Sonar: Checking Life (`ping`)

Know who (`ip addr`), know others out there.
Command **`ping`**.

`ping` sends small packet (ICMP Echo Request) address waits reply (ICMP Echo Reply). Submarine sonar.

### 2.1 Basic Ping
```bash
$ ping 8.8.8.8
```
*(8.8.8.8 Google DNS, always up. Standard check internet).*

Output:
`64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=14.5 ms`
`64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=14.2 ms`

**Read:**
1.  **icmp_seq:** Sequence. Jump 1 to 3, lost packet 2 (Packet Loss). Connection unstable.
2.  **time:** Latency. Time go California return.
    *   `< 10ms`: Same city fiber divine.
    *   `20-50ms`: Normal.
    *   `> 200ms`: Slow (satellite).
    *   `Request timeout`: No answer. Cable broken, Firewall block.

**Vital difference Windows!**
*   Windows `ping` stop 4.
*   Linux `ping` **infinite**. Until stop.
*   **STOP:** `Ctrl + C`.

### 2.2 Variations
*   **3 attempts:** `ping -c 3 google.com` (Count).
*   **Alive check:** Visual `ping` best.

@quiz: Run `ping 8.8.8.8` message "Network is unreachable"?
@option: Google down.
@correct: Computer no route internet (no IP Gateway).
@option: Cable unplugged.
@option: DNS fail.

@section: 3. Phonebook: DNS (`nslookup`, `dig`)

Computers love numbers (`142.250.200.14`).
Humans love names (`google.com`).

**DNS (Domain Name System)** translates Names Numbers. Internet phonebook.
No DNS, memorize IPs favorites.

### 3.1 Testing DNS
Internet ok (ping `8.8.8.8` works), browser fails. "Cannot resolve address".
DNS fail.

Diagnose, ask system: "Who is google.com?".

**Basic: `nslookup`**
```bash
$ nslookup google.com
Server:     127.0.0.53
Address:    127.0.0.53#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.200.14
```
Answer Address, DNS works.
"Servfail" "NXDOMAIN" hang 10s, DNS broken.

**Pro: `dig`**
Admins prefer `dig` (Domain Information Groper) details.
```bash
$ dig google.com
```
Look "ANSWER SECTION". Empty bad.

### 3.2 Who is my DNS?
Who asking?
Config sacred file: **`/etc/resolv.conf`**.

`cat /etc/resolv.conf`.
Line `nameserver X.X.X.X`.
*   Modern `systemd-resolved`, `127.0.0.53` (local proxy).
*   Classic, `1.1.1.1` (Cloudflare) `8.8.8.8` (Google).

### 3.3 File `/etc/hosts` (Pocket book)
Before world DNS, Linux looks pocket notebook `/etc/hosts`.
Manipulate reality.

Edit file (root) add:
`127.0.0.1   facebook.com`

Try Facebook, computer connect self (localhost) fail. Block sites test dev (`myweb.test` local).

@section: 4. Map: Routing Gateway (`ip route`)

IP. DNS ok. No internet.
Computer don't know **where exit door**.

Local net (home), computer talk neighbors street (subnet). Send letter China (Internet), give Postman leaves neighborhood.
Postman **Router** (Gateway).

### 4.1 Route Table
Write:
```bash
$ ip route
```
Output:
`default via 192.168.1.1 dev eth0 proto dhcp ...`
`192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.45`

Vital line first: **`default via ...`**.
*   **Default:** "Everything don't know (Internet)".
*   **Via 192.168.1.1:** "Send IP".

IP (`192.168.1.1`) Router.
Line missing, computer trapped local. No exit.

**Fire Test:**
Ping Gateway.
`ping 192.168.1.1`
*   Works: Connection router ok. Problem outside (ISP).
*   Fails: Problem PC Router (Bad WiFi, cable, router off).

@section: 5. Ports Sockets: Hotel Rooms

IP arrival packet computer. Which app deliver?
Email? Web? Minecraft?

**Ports**.
IP Building Address (Hotel). Ports Room Numbers.
*   Room 80: Web Server (HTTP).
*   Room 443: Secure Web (HTTPS).
*   Room 22: SSH Server.

### Who listening? (`ss` / `netstat`)
Connect rejected ("Connection Refused"). Program not running listening wrong port.

Modern **`ss`** (Socket Stat), old `netstat`.

```bash
# See TCP (t) listening (l) numeric (n)
$ ss -tln
```
Output:
`LISTEN   0   128   0.0.0.0:22    0.0.0.0:*`
"Someone (SSH) listening port 22 all IPs (0.0.0.0), waiting".

Run web server not see port 80, not running.

@section: 6. Emergency Protocol: Troubleshooting Flow

Boss screams: "Server no net!". Panic no. Follow algorithm. **OSI Bottom Up**.

**Step 1: Physical Link (Cable)**
*   Lights card?
*   Cmd: `ip link`.
*   `state UP` `DOWN`?
*   *Sol:* DOWN, check cable `sudo ip link set eth0 up`.

**Step 2: Network (IP)**
*   Identity?
*   Cmd: `ip addr`.
*   IP (inet 192...)?
*   *Sol:* No, ask router `sudo dhclient -v`.

**Step 3: Local Net (Neighborhood)**
*   See Router?
*   Cmd: `ip route` (see router) -> `ping 192.168.1.1`.
*   *Sol:* Fail, router off firewall block.

**Step 4: Internet (Outside)**
*   Exit?
*   Cmd: `ping 8.8.8.8` (Ping IP, not name).
*   *Sol:* Fail step 3 ok, ISP cut line router routing bad.

**Step 5: Name Resolution (DNS)**
*   Translate names?
*   Cmd: `ping google.com` `nslookup google.com`.
*   *Sol:* Step 4 ok (ping 8.8.8.8) fail, **ALWAYS DNS**. Edit `/etc/resolv.conf` put `nameserver 8.8.8.8`.

**Step 6: App (Service)**
*   All ok, web load fail.
*   Cmd: `curl -I google.com` `ss -tln`.
*   *Sol:* Web server down firewall (iptables/ufw) reject port 80.

@section: 7. Summary / Cheat Sheet

| Command | Action | Analogy |
| :--- | :--- | :--- |
| `ip addr` | IPs interfaces | ID Card |
| `ip link set eth0 up` | Turn on card | Wake bouncer |
| `ping 8.8.8.8` | Check IP conn | Sonar |
| `ip route` | Route table | Exit Map |
| `nslookup google.com` | Consult DNS | Phonebook |
| `dig google.com` | Consult DNS (Pro) | Interrogate operator |
| `ss -tln` | Listening ports | Open hotel rooms |
| `/etc/resolv.conf` | DNS Config | Phonebook list |
| `/etc/hosts` | Overwrite DNS | Private notebook |

Congrats! Know net better most. Next time "wifi down", blind reboot no. DNS, Gateway, Physical. Own packets.