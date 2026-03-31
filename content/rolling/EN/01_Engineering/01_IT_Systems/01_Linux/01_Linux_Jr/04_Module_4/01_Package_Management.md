@title: Package Management: The Everything Store (apt, dnf)
@icon: 🧩
@description: The definitive guide (+500 lines) to understanding software installation in Linux. Forget Google .exe. Master apt, dnf, repositories, and dependency hell.
@order: 1

# Software Arsenal: Package Management in Linux

@section: LPIC-1 map — Module 4 (packages, network, services, shell)

Maps to **LPIC-1 (102 / 109 / 108 / 105)**:

*   **102.3 Package managers:** `apt`, `dnf`/`yum`, `rpm`, `dpkg`; repositories; GPG keys on repos (concept).
*   **102.4 Compiling from source:** dev dependencies (`build-essential`, "Development Tools" groups).
*   **109.1 Networking fundamentals:** interfaces, DNS, diagnostics (`Basic_Networking`).
*   **108.1 Logging:** `journald`, file logs (`Logs` lesson).
*   **105.1 Shell scripting:** variables, tests, loops (`Intro_Scripting`).
*   **SSH:** remote access (`Remote_Access`)—core for any hands-on exam lab.

Welcome to the lesson changing how you understand software installation.

Windows brain habit:
1.  Need program (Firefox).
2.  Open browser.
3.  Google "download firefox".
4.  Enter web (hoping official not malware).
5.  Find "Download".
6.  Download `.exe` `.msi`.
7.  Double click "Next, Next, Next".

**Linux considers this barbaric, insecure, obsolete.**

Linux invented "App Store" decades before iPhone. Software managed **centrally**.
Don't search software; ask system fetch.

Massive guide, dissect **Package Manager**. Safer, repositories, dependencies solved auto, handle `apt` (Debian/Ubuntu) `dnf` (RedHat/Fedora) pro.

@section: 1. Paradigm Shift: What is a Package?

**Package** compressed file (.zip) contains:
1.  **Binaries:** Program executable.
2.  **Config:** Defaults `/etc`.
3.  **Metadata:** Critical info manager:
    *   Name Version.
    *   Description.
    *   **Dependencies:** "Need library X and Y".

### Dependency Hell
Old times (Windows sometimes), install program, fails missing `.dll` `.so`. Search manual, install, pray version correct.

**Package Manager** solves math.
Say: *"Install VLC Player"*
Manager thinks: *"VLC needs `libvideo`, `libsound`, `qt-interface`. `qt-interface` needs `libgraphics`. Download install 5 things correct order auto"*.

### Repositories: Source of Truth
Instead download web, Linux list **Repositories**. Servers maintained distro creators (Canonical, Red Hat, Community) thousands packages verified signed.

Package not repo, system "blind".

@section: 2. APT: Debian Ubuntu Giant

Ubuntu, Mint, Debian, Kali, Pop!_OS, tool **APT** (Advanced Package Tool).
Extension **`.deb`**.

### 2.1 Sacred List: `sources.list`
Before install, computer needs know where look. List text file.

```bash
$ cat /etc/apt/sources.list
```
Lines:
`deb http://archive.ubuntu.com/ubuntu/ jammy main restricted`

*   **deb:** Repo binaries.
*   **url:** Web server.
*   **jammy:** Code name Ubuntu version (22.04).
*   **main/restricted:** Sections.

**Important:** Add new repo (PPA), add line file or create new `/etc/apt/sources.list.d/`.

### 2.2 Updating Catalog (`apt update`)
Error #1 newbies.
`sudo apt update` **DOES NOT UPDATE SOFTWARE**.

Repeat: `apt update` **NO** install new.

Does:
1.  Downloads recent package list servers.
2.  Compares list computer.
3.  Notes updated versions available.

Like downloading supermarket catalog before buying. Don't do, computer try download old versions not exist server "Error 404".

**Rule:** Run `sudo apt update` always before install.

### 2.3 Updating System (`apt upgrade`)
Command **YES** installs updates.

```bash
$ sudo apt upgrade
```
*   Looks update list (thanks `update`).
*   Says: "Download 500MB. Continue?".
*   Downloads installs new versions programs Kernel.

### 2.4 Installing Software (`apt install`)
Install new:

```bash
$ sudo apt install firefox
```
Multiple:
```bash
$ sudo apt install vlc gimp htop neofetch
```

Installed already? `apt` attempts update latest. Latest? Nothing.

### 2.5 Searching (`apt search` `apt show`)
Don't know name. `python` `python3`?

```bash
$ apt search python3
```
Huge list.

Details specific before install:
```bash
$ apt show python3
```
Version, size, desc, depends.

### 2.6 Removing (`remove` vs `purge`)
Two ways delete.

1.  **`remove`:** Uninstall program, **LEAVE** config files (`/etc`).
    *   *Use:* Might reinstall future keep config.
    ```bash
    $ sudo apt remove nginx
    ```

2.  **`purge`:** Uninstall **DELETE** global config.
    *   *Use:* Eliminate completely.
    ```bash
    $ sudo apt purge nginx
    ```
    *(Note: usually not delete home hidden folders).*

### 2.7 Cleaning Trash (`autoremove`)
Install `vlc`, 20 libs installed.
Uninstall `vlc`, 20 libs stay "orphans". Nobody needs, space.

APT warns: *"The following packages were automatically installed and are no longer required..."*.

Clean:
```bash
$ sudo apt autoremove
```
Satisfying free MBs.

@quiz: Critical difference `apt update` and `apt upgrade`?
@option: `update` kernel `upgrade` apps.
@correct: `update` refreshes package list (catalog), `upgrade` installs new versions.
@option: Same, `update` old name.
@option: `upgrade` premium users.

@section: 3. DNF: Red Hat Power

Fedora, Red Hat (RHEL), CentOS, AlmaLinux, manager **DNF** (Dandified YUM).
Extension **`.rpm`**.

DNF modern (better deps history), commands identical.

| Action | APT | DNF |
| :--- | :--- | :--- |
| Refresh | `apt update` | `dnf check-update` |
| Update | `apt upgrade` | `dnf upgrade` |
| Install | `apt install pkg` | `dnf install pkg` |
| Delete | `apt remove pkg` | `dnf remove pkg` |
| Search | `apt search pkg` | `dnf search pkg` |
| Info | `apt show pkg` | `dnf info pkg` |

**Unique DNF: History**
DNF logs everything. Install break system? Undo.

```bash
$ sudo dnf history
$ sudo dnf history undo 5
```
*(Undoes transaction 5. Marvel).*

@section: 4. Universal Packages: Snap, Flatpak, AppImage

APT DNF problem **fragmentation**.
Create program, package `.deb` Ubuntu, `.rpm` Fedora, Arch. Work. Lib versions conflict.

**Universal Packages**. Work ANY Linux.

### 4.1 Flatpak (Community Fav)
*   **Philosophy:** Decentralized.
*   **Work:** Sandbox. Own libs, not system.
*   **Store:** Flathub.org.
*   **Pros:** Secure, latest app version independent Linux version.
*   **Cons:** Disk space (duplicate libs).

```bash
$ flatpak install flathub org.gimp.GIMP
$ flatpak run org.gimp.GIMP
```

### 4.2 Snap (Canonical/Ubuntu)
*   **Philosophy:** Centralized (Canonical).
*   **Work:** Similar Flatpak, server kernel services too.
*   **Pros:** Easy Ubuntu. Auto updates invisible.
*   **Cons:** Server proprietary. Startup slow. Loop devices `lsblk`.

```bash
$ sudo snap install spotify
```

### 4.3 AppImage (Portable)
*   **Philosophy:** "One file, one app".
*   **Work:** Download `.AppImage`. Exec permission (`chmod +x`). Run.
*   **Pros:** No install. Like portable `.exe`. Test software clean.
*   **Cons:** No auto update. Download manual.

@quiz: Main advantage Flatpak/Snap vs native (.deb/.rpm)?
@option: Lighter faster.
@option: Better kernel integration.
@correct: Work any distro carry own dependencies avoiding conflicts.
@option: No internet needed.

@section: 5. Manual Install: `dpkg` `rpm`

Program not repo, developer offers `.deb` (Chrome, VS Code, Discord).

### Installing loose `.deb`
Can't `apt install file.deb` direct (old versions). Need low level: **`dpkg`**.

```bash
# Download
$ wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

# Install (error deps likely)
$ sudo dpkg -i google-chrome-stable_current_amd64.deb

# Fix broken deps
$ sudo apt install -f
```
`apt install -f` (fix-broken) magic. Sees tried install Chrome failed missing libs, downloads installs completes.

**Modern Method:**
Recent `apt`, give file path handles all (uses `dpkg` solves deps):
```bash
$ sudo apt install ./google-chrome-stable_current_amd64.deb
```
*( `./` mandatory know local file not repo package).*

@section: 6. Compiling Source (The Hard Way)

90s style. Rare today unless specific software or yesterday version.

Trinity:
1.  **Download source:** (`git clone ...` `.tar.gz`).
2.  **`./configure`:** Script checks system tools libs ready compile. Fail here missing.
3.  **`make`:** Compile. Text (C/C++) binary. CPU 100% time.
4.  **`sudo make install`:** Copy binaries system folders (`/usr/local/bin`).

**Avoid novice:**
*   Manager **NO know** installed.
*   No auto update.
*   Hard uninstall (unless keep source `sudo make uninstall`).
*   Dirty system.

@section: 7. Troubleshooting

### Error: "Could not get lock /var/lib/dpkg/lock"
Trying install see:
`E: Could not get lock ... held by process 1234 (apt)`

**Cause:** Only **ONE** `apt` process allowed system.
1.  Another terminal install.
2.  Auto update background.
3.  Closed terminal brute install zombie lock.

**Sol:**
1.  Wait. Auto update.
2.  Kill: `sudo kill 1234`.
3.  Delete lock (rare): `sudo rm /var/lib/dpkg/lock-frontend` then `sudo dpkg --configure -a`.

### Error: "404 Not Found" update
Old Ubuntu Fedora "End of Life" (EOL) repos moved history, or PPA dead.
**Sol:** Disable repo `/etc/apt/sources.list` or upgrade distro supported.

### Error: "Held Broken Packages"
Mixed repos version mess (A needs B v1, C needs B v2).
**Sol:** `sudo apt install -f`. Or `aptitude` (smarter interface) proposes math solutions (delete A, update C).

@section: 8. Practical Lab: Software Management

Practice cycle.

1.  **Update catalog:**
    ```bash
    $ sudo apt update
    ```

2.  **Install fun (`cmatrix`):**
    ```bash
    $ sudo apt install cmatrix
    ```

3.  **Run:**
    ```bash
    $ cmatrix
    ```
    *(Neo Matrix. `q` `Ctrl+C` exit).*

4.  **Verify:**
    ```bash
    $ which cmatrix
    $ dpkg -L cmatrix  # (Show files created)
    ```

5.  **Uninstall:**
    ```bash
    $ sudo apt remove cmatrix
    ```

6.  **Clean:**
    ```bash
    $ sudo apt autoremove
    ```

7.  **Install external .deb (Opt):**
    Download "VS Code" "Chrome" web install `sudo apt install ./file.deb`.

@section: Summary / Cheat Sheet (APT)

| Action | Command | Explanation |
| :--- | :--- | :--- |
| **Refresh** | `sudo apt update` | Download list. NO install. |
| **Update** | `sudo apt upgrade` | Install new versions. |
| **Install** | `sudo apt install [pkg]` | Install program. |
| **Reinstall** | `sudo apt reinstall [pkg]` | If deleted file error. |
| **Delete** | `sudo apt remove [pkg]` | Delete program, keep config. |
| **Purge** | `sudo apt purge [pkg]` | Delete program AND config. |
| **Clean** | `sudo apt autoremove` | Delete orphan deps. |
| **Search** | `apt search [text]` | Search description. |
| **Info** | `apt show [pkg]` | Tech details. |
| **Fix** | `sudo apt install -f` | Fix broken install. |

Congrats! Manage software secure clean pro. Never download shady `.exe`.