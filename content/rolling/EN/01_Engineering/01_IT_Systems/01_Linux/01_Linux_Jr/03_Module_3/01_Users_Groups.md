@title: Identity Management: The Ultimate Guide to Users and Groups
@icon: 👥
@description: The bible of account administration in Linux. From anatomy of /etc/passwd to permission management with sudo, explained step by step for beginners.
@order: 1

# The Multi-User Castle: Identity Management in Linux

@section: LPIC-1 map — Module 3 (accounts, permissions, processes)

Typical **LPIC-1 (107 / 103 / 104)** alignment:

*   **107.1 Local users and groups:** `/etc/passwd`, `/etc/shadow`, `/etc/group`, `useradd`, `usermod`, `groupadd`, passwords (`passwd`, `chage`).
*   **107.2 Automation (foundation):** cron/at references (deep dive links to scripting module and advanced course).
*   **104.5 Standard permissions:** `chmod`, `chown`, `umask` (dedicated lesson).
*   **103.4 Processes:** `ps`, signals, basics of `top`/`htop`.
*   **Local security:** `sudo`, `/etc/sudoers` via `visudo`, PAM mentioned; SELinux/AppArmor reserved for advanced track.
*   **RHEL:** `wheel` group; **Debian/Ubuntu:** `sudo` group—know both.

Welcome to Module 3. Here is where you stop being a simple user and become an **Administrator**.

If you come from Windows (home versions), you are probably used to your PC being "yours". You turn it on, enter, and do what you want. You are the owner.
In Linux (and Unix), philosophy is radically different. Linux was designed in 70s for mainframes: gigantic and expensive computers that had to be shared by hundreds of people at once.

Imagine Linux is not a single-family house, but a **Corporate Skyscraper**.
*   There must be a **Supreme Concierge** with keys to everything (Root).
*   There are **Employees** who have key to their office but not boss's office (Users).
*   There are **Cleaning Staff** who have keys to hallways but not safes (Groups).
*   There are **Robots** of maintenance working in basements (System Users).

In this massive lesson, we will learn to manage all inhabitants of this skyscraper. We will create digital lives, group them, give them powers, and finally, learn to eliminate them without trace.

@section: 1. System Actors

Before typing commands, we need to understand Linux social castes.

### 1.1 The God: Root (UID 0)
In Linux, there is special account called `root`.
`root` is not just administrator like in Windows. `root` is God.
*   Can read any file (even private ones of others).
*   Can delete anything (even operating system itself while running).
*   Does not obey permission rules.

System identifies `root` not by name, but by ID number 0 (UID 0). Any user with UID 0 is God.

### 1.2 The Citizens: Normal Users (UID 1000+)
They are humans. You, me, your coworkers.
*   Live in `/home/username`.
*   Only have power inside own home. Cannot install programs for everyone, change system time, or view other users' files.
*   Usually start counting from UID 1000 in most distros (Ubuntu, Fedora).

### 1.3 The Invisible: System Users (UID 1-999)
This confuses newbies a lot. If you do `cat /etc/passwd`, you will see dozens of users you don't know: `www-data`, `lp`, `mail`, `nobody`, `daemon`, `sshd`.
Have you been hacked? **No.**

They are accounts created for **programs** (services) to run.
*   **Security:** Web server (Apache or Nginx) does not run as `root`. Runs as user `www-data`. If hacker breaks your web server, only gets `www-data` powers, which are very limited. Does not get total system control.
*   These users **have no password** and **cannot login** (no shell). They are worker robots.

@section: 2. The Three Sacred Books

In Windows, users are stored in hidden complex binary database (SAM).
In Linux, like everything, **they are plain text files**. You can read them. Must understand them.

These three files in `/etc` folder are heart of system identity.

### Book 1: `/etc/passwd` (The Census)
Contains list of all users. Public (anyone can read, `cat /etc/passwd`).
Each line represents user and has 7 fields separated by colons `:`.

**Example:**
`john:x:1001:1001:John Doe,HR,,:/home/john:/bin/bash`

Let's dissect:
1.  **Username (`john`):** Name to login.
2.  **Password (`x`):** Formerly encrypted password was here. Nowadays `x` means "Password stored securely in `/etc/shadow`, not here".
3.  **UID (`1001`):** User ID. Real number Kernel uses to identify user.
4.  **GID (`1001`):** Group ID. Primary group John belongs to.
5.  **GECOS (`John Doe,HR,,`):** Extra info (Full name, dept, phone). Optional and purely informational.
6.  **Home (`/home/john`):** Where user lands upon entry.
7.  **Shell (`/bin/bash`):** What program runs upon entry. If put `/bin/false` or `/sbin/nologin` here, entry forbidden.

### Book 2: `/etc/shadow` (The Secrets)
Contains passwords. **Only root can read.** It is the vault.

**Example:**
`john:$6$xyz...:19850:0:99999:7:::`

Quick dissection:
1.  **Username:** John.
2.  **Hash ($6$xyz...):** Encrypted password. If see `*` or `!`, account locked (no password).
3.  **Last Change:** Days since 1970 password changed.
4.  **Min Days:** Minimum days before changing again.
5.  **Max Days:** Days before expiration (security policy).
6.  **Warning:** Warning days before expiration.

### Book 3: `/etc/group` (The Guilds)
Defines groups.
`developers:x:2000:john,mary,peter`

1.  **Group Name:** developers.
2.  **Password:** (Almost never used, `x`).
3.  **GID:** Numeric Group ID.
4.  **Members:** List of users belonging to this *secondary* group.

@quiz: Why does an 'x' appear in password field of `/etc/passwd`?
@option: Because user has no password.
@correct: Because real (encrypted) password moved to `/etc/shadow` for security.
@option: Because user is locked.
@option: System error.

@section: 3. Creating Life: `useradd` and `adduser`

To create user, we don't edit files manually (too risky). Use commands.
Eternal doubt arises: Use `useradd` or `adduser`?

### The Raw Tool: `useradd`
Standard command, universal in all Linuxes. Low-level binary.
**Problem:** Dumb. By default, creates user but **NOT** `/home` folder, no password, assigns poor shell (sometimes `sh` instead of `bash`).

If use `useradd`, must give precise instructions:
```bash
# Create Peter, creating home (-m), specifying shell (-s) and comment (-c)
$ sudo useradd -m -s /bin/bash -c "Peter form Workshop" peter
```
If forget `-m`, Peter exists, but homeless. Login error.

### The Friendly Tool: `adduser`
Script (common in Debian/Ubuntu) using `useradd` underneath but asks interactive questions.
```bash
$ sudo adduser mary
```
System asks:
1.  Password for Mary.
2.  Full Name.
3.  Room Number.
4.  Phone.
Automatically creates `/home`, configures permissions, leaves everything ready.
**Recommendation:** If human, use `adduser`. If automatic script, use `useradd`.

### Assigning Password: `passwd`
If used `useradd`, user exists but no password (locked). Must give one.
```bash
$ sudo passwd peter
```
Can also change own password simply typing `passwd`.

**Security Trick:**
Can lock account temporarily (e.g., employee on vacation) without deleting.
*   Lock: `sudo passwd -l peter` (Lock).
*   Unlock: `sudo passwd -u peter` (Unlock).

@section: 4. User Surgery: `usermod`

People change. Move depts, change name, need new permissions. `usermod` allows editing existing account.

### Add to Group (Most used command)
Imagine want Peter to use Docker. Must add to `docker` group.
Common error is using `-G` (Groups) without `-a` (Append). If do that, **remove Peter from all other groups** and put only in Docker.

**Correct Syntax (Tattoo it):**
`usermod -aG [GROUP] [USER]`
*   `-a`: Append (Add to what already has).
*   `-G`: Secondary groups.

```bash
$ sudo usermod -aG sudo,docker peter
```

### Other common operations
*   **Change username (Login):**
    `sudo usermod -l new_name old_name`
*   **Change Home folder:**
    `sudo usermod -d /home/new_home -m peter`
    *(Option `-m` vital: moves files from old to new. Without it, only config changes but files left behind).*
*   **Change Shell:**
    `sudo usermod -s /bin/zsh peter`

@section: 5. The Executioner: `userdel`

When employee leaves, eliminate account.
```bash
$ sudo userdel peter
```
**Careful!** Removes user from census (`/etc/passwd`), but **leaves `/home/peter` folder intact** on disk. Done for security, not to lose documents.

If want to delete user AND files (full wipe), use `-r` (remove home):
```bash
$ sudo userdel -r peter
```

@section: 6. Group Management

Groups are way to organize collective permissions.
Imagine folder `/srv/project` where 5 programmers must work.
Don't give permissions to each.
1.  Create group: `sudo groupadd programmers`.
2.  Add users: `sudo usermod -aG programmers ana`.
3.  Change group owner of folder: `sudo chown :programmers /srv/project`.
4.  Give group permissions: `sudo chmod g+w /srv/project`.

Ready! Now anyone in group can work there.

**Group Commands:**
*   `groupadd`: Create group.
*   `groupdel`: Delete group.
*   `groups [user]`: See groups user belongs to.
*   `id [user]`: Complete tech info (UID, GID, Groups).

@quiz: What option is critical when using `usermod` to add user to secondary group without removing from existing groups?
@option: -G
@correct: -a
@option: -u
@option: -A

@section: 7. SUDO: The Ring of Power

In old days, for admin tasks, did this:
1.  Type `su` (Switch User).
2.  Enter Root password.
3.  Became God.
4.  Did work.
5.  Exited.

Dangerous. If leave root session open, or run virus as root, game over. Also, had to share root password with all admins.

**Solution: SUDO (SuperUser DO)**
Sudo allows normal user to "borrow" root powers for **single command**, using **own password**.

### Sudo Advantages
1.  **Accountability:** Everything done with sudo logged (`/var/log/auth.log`). Know *who* broke server.
2.  **Security:** Don't share root key.
3.  **Granularity:** Configure sudo so John only runs `apt update` and nothing else.

### File `/etc/sudoers`
Defines who can use sudo and what.
**NEVER** edit with normal editor (`nano` or `vi`). Syntax error **breaks sudo**, nobody can fix because need sudo to fix.

**Always use:**
```bash
$ sudo visudo
```
Opens file, lets edit, and **verifies syntax before saving**. If error, doesn't save, saving your life.

**Typical config:**
To give full permissions, usually add to `sudo` group (Debian/Ubuntu) or `wheel` (RedHat/Fedora).
```bash
# Give admin powers to Ana
$ sudo usermod -aG sudo ana
```

@section: 8. Practical Lab: Hiring Intern

Simulate real scenario. "Luis", new dev intern arrives.
Goals:
1.  Create user.
2.  Assign `developers` group.
3.  Don't give `sudo` permissions (intern, don't want breakage).
4.  Force password change on first login.

**Step 1: Create dev group**
```bash
$ sudo groupadd developers
```

**Step 2: Create user**
```bash
$ sudo useradd -m -s /bin/bash -c "Luis Intern" -G developers luis
```
*(Notice using capital -G to add secondary group directly at birth).*

**Step 3: Assign temp password**
```bash
$ sudo passwd luis
(Type: welcome123)
```

**Step 4: Force password change (Expire)**
```bash
$ sudo chage -d 0 luis
```
*(`chage` manages expiry. `-d 0` sets last change date to day 0 (1970), making system think password expired immediately).*

**Step 5: Verification**
```bash
$ id luis
uid=1002(luis) gid=1002(luis) groups=1002(luis),1003(developers)
```
See main group (luis) and secondary (developers). Not in `sudo`. Perfect!

@section: Summary / Cheat Sheet

| Action | Command | Notes |
| :--- | :--- | :--- |
| **Create User** | `useradd -m -s /bin/bash user` | Use `-m` to create HOME. |
| **Create User (Easy)** | `adduser user` | Interactive (Ubuntu/Debian). |
| **Delete User** | `userdel -r user` | Use `-r` to delete HOME. |
| **Change Password** | `passwd user` | `-l` lock, `-u` unlock. |
| **Modify User** | `usermod` | `-aG` add groups. |
| **Create Group** | `groupadd group` | |
| **See Info** | `id user` | Shows UID, GID, groups. |
| **Edit Sudoers** | `visudo` | **NEVER** use direct nano. |

Mastering users is base of Linux security. Remember: Principle of Least Privilege. Give each user only power needed, not one bit more.