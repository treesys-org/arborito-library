@title: Remote Access: The Master Key (SSH)
@icon: 🔐
@description: The definitive guide (+500 lines) to controlling computers across the world. Learn encryption, public/private keys, tunnels, and server management like a pro.
@order: 3

# Digital Telepathy: Mastering SSH (Secure Shell)

Welcome to the most important tool in Internet history.

If following course, learned terminal own computer. Good, real world no.
Professional world, computers manage (web servers, DB, supercomputers) not desk. No monitor. No keyboard. Rack data center Virginia, Frankfurt, Singapore. Cold noisy metal boxes stacked thousands.

Control machine 5,000 km distance sitting front?

Answer **SSH (Secure Shell)**.

SSH not program; protocol. Magic tube, invisible indestructible connects keyboard remote brain.
Before SSH, **Telnet**. Telnet passwords commands plain text cables. Sniffer read password. Postcard credit card.
SSH, born 1995, postcard armored truck, tanks, tunnel. End-to-end encryption.

Massive guide, "connect" -> Crypto Master. Keys not passwords, jump servers, secret tunnels bypass firewalls, transfer files secure.

Prepare. Hack planet (legally).

@section: 1. First Connection: Handshake

Connect other computer, need 3 things:
1.  **Client:** Your PC (Linux Mac have. Windows PowerShell PuTTY).
2.  **Server:** Remote machine `openssh-server` running.
3.  **Address:** IP Domain.

### Basic Syntax
Simple:

`ssh [USER]@[SERVER]`

Example:
```bash
$ ssh john@192.168.1.50
```
*(Trans: "Hi, john. Enter 192.168.1.50").*

### First Encounter (Fingerprint)
**First time** connect new server, scary message. Crucial understand.

```text
The authenticity of host '192.168.1.50 (192.168.1.50)' can't be established.
ED25519 key fingerprint is SHA256:RO42/uZq...
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

**What happening?**
SSH paranoid. Avoid **Man-in-the-Middle (MITM)**.
*   Think connect bank.
*   Hacker intercepts cable presents fake server.
*   Type password hacker keeps.

Avoid, server SSH **Fingerprint** unique crypto key.
Message, PC says:
*"Hey, never spoke 192.168.1.50. Says ID `RO42/uZq...`. Trust?"*

Max security, call admin: *"Hey, server fingerprint?"*. Match `yes`.

### File `known_hosts`
Type `yes`, SSH saves fingerprint file: `~/.ssh/known_hosts`.
Now, SSH remembers `192.168.1.50` fingerprint `RO42...`. No ask.

**RED ALERT! "Remote Host Identification Has Changed"**
Connect same server big red screen:
```text
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
```
Fingerprint changed.
*   **Option A (Innocent):** Admin reinstalled OS. New prints.
*   **Option B (Attack):** Intercepted fake server.

Know A, delete old print `known_hosts`:
```bash
$ ssh-keygen -R 192.168.1.50
```

@section: 2. Authentication: Death of Passwords

Default, SSH asks user password.
Typing passwords:
1.  **Slow:** Type every time.
2.  **Insecure:** Guess (brute force) steal.

Pros use **Asymmetric Cryptography (Key Pair)**.

### Lock Key Metaphor
Two pieces:
1.  **Public Key (Lock):** Millions copies give everyone. *Close* things. Not open.
2.  **Private Key (Master Key):** Only one. Never give. Keep pillow. *Open* closed public lock.

**Auth process no password:**
1.  Generate pair (Key Lock) PC.
2.  Go server put **Lock (Public)** door.
3.  Try enter, server sees lock says: *"Only pass key owner"*.
4.  PC uses **Key (Private)** prove math owner, no send key net.
5.  Enter!

### Step 1: Generate keys (`ssh-keygen`)
PC (not server):
```bash
$ ssh-keygen -t ed25519 -C "email@example.com"
```
*   `-t ed25519`: Modern algo (fast secure RSA).
*   `-C`: Comment ID key (email).

Ask save. Enter (default).
Ask **Passphrase**.
*   *Rec:* Password here. Encrypts private key disk. Steal laptop, no use key without phrase.

Now two files `~/.ssh/`:
*   `id_ed25519`: **PRIVATE KEY. NEVER SHARE.**
*   `id_ed25519.pub`: Public Key (Lock). Distribute.

### Step 2: Install Lock (`ssh-copy-id`)
Put lock server. Magic command:

```bash
$ ssh-copy-id john@192.168.1.50
```
Ask password ("old" user) last time enter paste key.
Finish, try:

```bash
$ ssh john@192.168.1.50
```
Boom! In no password (maybe passphrase key local).

@quiz: File NEVER share email?
@option: id_ed25519.pub
@option: known_hosts
@correct: id_ed25519 (Private key)
@option: config

@section: 3. File `config`: Contacts

Connect tedious: `ssh dev@server-aws-prod-01.corp.com -p 2222`.
Type `ssh pro`?

Yes.
Folder `~/.ssh/`, create `config`.

```bash
$ nano ~/.ssh/config
```

Define "alias":

```text
Host pro
    HostName server-aws-prod-01.corp.com
    User dev
    Port 2222
    IdentityFile ~/.ssh/id_work

Host home
    HostName 192.168.1.50
    User john

Host *
    User root
```

**Analysis:**
*   Write `ssh pro`, SSH reads, "pro" connect long host, user, port, key.
*   `ssh home`, connect local john.
*   `Host *` apply all (default root).

Save million keystrokes year.

@section: 4. `scp` `sftp`: Teleport Files

SSH not just commands. Move data. Safe tunnel.

### `scp` (Secure Copy)
Same `cp` local, network.

**Struct:**
`scp [SRC] [DST]`

**Upload (PC to Server):**
```bash
$ scp report.pdf john@192.168.1.50:/home/john/docs/
```
*(Colon `:` after IP. Sep address path).*

**Download (Server to PC):**
```bash
$ scp john@192.168.1.50:/var/log/syslog .
```
*(Dot end "save here").*

**Copy folder:**
`-r` (Recursive).
```bash
$ scp -r photos john@192.168.1.50:/home/john/
```

### `sftp` (Secure FTP)
Navigate, see, upload/down multiple, `scp` annoying. `sftp`.
Interactive.

```bash
$ sftp john@192.168.1.50
sftp> ls        (List SERVER)
sftp> lls       (List YOUR PC)
sftp> get file.txt  (Download)
sftp> put photo.jpg (Upload)
sftp> bye       (Exit)
```
Graphical FTP (FileZilla) support SFTP. Port 22.

@section: 5. SSH Tunnels: Dark Magic

Wizard.
Scenario:
*   DB Server (MySQL) office.
*   Security firewall blocks 3306 (MySQL) internet.
*   Open 22 (SSH).

Connect DB home graphic tool?
Answer: **SSH Tunnel (Local Port Forwarding).**

Tell SSH: *"Hey, take port 9000 MY PC, tube SSH, exit connect port 3306 server"*.

**Command:**
```bash
$ ssh -L 9000:localhost:3306 user@office-server
```
*   `-L`: Local forwarding.
*   `9000`: PC port.
*   `localhost`: Dest *server perspective* (server connects self).
*   `3306`: Dest port.

**Result:**
Config MySQL client `localhost:9000`.
Magic! Data 9000, encrypt SSH, exit server enter DB. "Bypass" firewall secure.

@quiz: `scp` download file server current folder, forgot dot `.` end. What happens?
@option: Download /tmp.
@option: Fail help.
@correct: Fail or unexpected missing dest arg.
@option: Download Home.

@section: 6. Hardening: Shield Server

Server SSH internet (22), **minutes** bots attack guess password (`root/123456`). Thousands.

Protect. Config `/etc/ssh/sshd_config`.

### 3 Security Commandments

1.  **Forbid Root:**
    Never enter root direct. User then `sudo`.
    *   Edit `/etc/ssh/sshd_config`:
    *   `PermitRootLogin no`

2.  **Disable Passwords:**
    Keys working, off passwords. Hacker guess key, no entry.
    *   `PasswordAuthentication no`

3.  **Change Port (Opt):**
    Move 22 to 2222 reduce noise bots (hacker find anyway).
    *   `Port 2222`

**Apply:**
Restart service:
```bash
$ sudo systemctl restart ssh
```
*Careful! Keep session open check OTHER terminal enter, or lock out.*

### Fail2Ban
Vital program. `sudo apt install fail2ban`.
Watch logs. IP fail 5 times min, Firewall **ban** IP hour. Bodyguard.

@section: 7. Troubleshooting

Fail connect. Ask pass shouldn't.
Diag **`-v` (Verbose)**.

```bash
$ ssh -v john@server
```
Show technical:
1.  "Connecting to..."
2.  "Server offered these authentication methods..."
3.  "Trying private key..."

More detail `-vv` `-vvv`.

**Common: "Permissions are too open"**
SSH strict keys. Private key read "others" (anyone PC steal), SSH refuse.
*   **Sol:**
    ```bash
    $ chmod 700 ~/.ssh
    $ chmod 600 ~/.ssh/id_ed25519
    ```
    (Only me folder, only me key).

@section: Summary / Cheat Sheet

| Action | Command |
| :--- | :--- |
| **Connect** | `ssh user@host` |
| **Gen Keys** | `ssh-keygen -t ed25519 -C "email"` |
| **Send Key** | `ssh-copy-id user@host` |
| **Copy to** | `scp file user@host:/path` |
| **Copy from**| `scp user@host:/path/file .` |
| **SFTP** | `sftp user@host` |
| **Debug** | `ssh -v user@host` |
| **Config Client** | `~/.ssh/config` |
| **Config Server** | `/etc/ssh/sshd_config` |

SSH umbilical cord admin. Care, protect, learn. Separates desktop user systems engineer.