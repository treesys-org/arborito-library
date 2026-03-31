@title: Permissions and Ownership: The System Guardian (chmod, chown)
@icon: 🔐
@description: The definitive guide (+500 lines) to understanding Linux security. Octal math, special bits (SUID, Sticky), and why 'chmod 777' is a deadly sin.
@order: 2

# The Castle Guardian: Mastering Permissions and Ownership

Welcome to the most important lesson for your system's security.

In Windows (especially home versions), we are used to "I am the owner of my PC". If I want to delete a file, I delete it. If I want to install a game, I install it. Windows assumes the user sitting at the keyboard is master of the universe.

**Linux trusts no one.**

Linux was designed as multi-user system from birth. Imagine university server in 1980: single giant computer, 500 students connected at once.
*   What prevents Student A reading Student B's thesis?
*   What prevents Student C deleting Professor's work?
*   What prevents malicious program breaking OS?

Answer is one: **PERMISSIONS.**

Every file, folder, device (remember, in Linux everything is file) has "bouncer" at entrance. Bouncer has strict list. Before letting you do anything (`read`, `write`, `execute`), bouncer checks ID (`UID`), checks list, decides if pass or kick (`Permission Denied`).

In massive lesson, we dissect system. Learn reading permission matrix (`ls -l`), changing laws (`chmod`), transferring ownership (`chown`), and understanding dark arts of special permissions (`SUID`, `Sticky Bit`).

Prepare. We go deep.

@section: 1. The Matrix: Understanding `ls -l`

Used `ls -l` previous module, but glanced. Now microscope.

Open terminal write:
```bash
$ ls -l /etc/shadow
-rw-r----- 1 root shadow 1425 Oct 20 10:00 /etc/shadow
```

Focus on first column, cryptic 10 chars string:
**`-rw-r-----`**

String is file security map. Divided 4 logical parts:

`[TYPE] [OWNER] [GROUP] [OTHERS]`

### 1.1 First Char: Type
Tells **what** is.
*   **`-` (Dash):** Normal file (text, image, executable, music).
*   **`d` (Directory):** Folder.
*   **`l` (Link):** Symbolic link (shortcut).
*   **`c` / `b`:** Special devices (Character/Block). Seen in `/dev`.

### 1.2 The Three Trios (Holy Trinity)
Next 9 chars grouped by 3.
Each trio represents permissions of different entity.

1.  **Chars 2-4 (OWNER / User):** What file owner can do.
2.  **Chars 5-7 (GROUP):** What any user in owner group can do.
3.  **Chars 8-10 (OTHERS):** What rest of world (not owner/group) can do.

Back to example: `-rw-r-----`
1.  Type: `-` (File).
2.  Owner (`rw-`): Read Write.
3.  Group (`r--`): Read.
4.  Others (`---`): Do NOTHING.

Means `root` (owner) can edit. Group `shadow` can read (useful verify passwords). You, mortal user, cannot even see. Secure.

@section: 2. R, W, X: What really mean?

People confused here.
`r` (Read), `w` (Write), `x` (Execute).
Seems obvious?
For **file**, yes.
But for **directory (folder)**, meaning changes subtly but vital understand.

### Case A: Permissions on FILES

*   **`r` (Read):** Can open file see content (`cat`, `less`, editor).
    *   *Without:* System says "Permission denied".
*   **`w` (Write):** Can modify content, truncate (empty), write over.
    *   *NOTE:* `w` on file **NOT** give permission delete file. To delete file, need permission on *folder* containing it, not file itself.
*   **`x` (Execute):** Can tell system try run file as program/script.
    *   *Note:* Windows executable if `.exe`. Linux extension irrelevant. Executable if `x` bit.

### Case B: Permissions on DIRECTORIES (Folders)

Logic changes. Folder is "container".

*   **`r` (Read):** Can list content (`ls`). See file names inside.
    *   *Without:* `ls` error.
*   **`w` (Write):** Can **create** and **delete** files inside folder.
    *   *Danger:* If have `w` on folder, can delete any file inside, **even if file not yours and no write permission on it**. Owner of container, can trash content.
*   **`x` (Execute / Pass):** Hardest understand. In folder, `x` means **"Enter Permission"** or "Traverse".
    *   Allows `cd` inside.
    *   Allows access file metadata inside (if know names).
    *   *Typical Combo:* If `r` but no `x`, can `ls` see names, but not `cd` or read file size/date. See question marks `????`.

**Vital Summary:** To use folder normally, need **`r-x`** (see and enter).

@quiz: Folder `SECRETS` permissions `rwxr--r--` (Owner: rwx, Group: r--, Others: r--). You "Other" user. Can `cd SECRETS`?
@option: Yes, have read (r).
@correct: No, need execute (x) on directory to enter.
@option: Yes, but cannot ls.
@option: Depends content.

@section: 3. `chmod`: The Legislator (Symbolic Mode)

Command `chmod` (**Ch**ange **Mod**e) tool change permissions.
Two modes. Start **Symbolic Mode**, intuitive humans (letters).

**Syntax:**
`chmod [WHO] [OPERATOR] [PERMISSION] file`

### Actors (Who)
*   `u`: **User** (Owner).
*   `g`: **Group**.
*   `o`: **Others**.
*   `a`: **All** (u+g+o).

### Operators
*   `+`: Add permission (leave others).
*   `-`: Remove permission.
*   `=`: Set exactly permissions (erase previous).

### Permissions
*   `r`, `w`, `x`.

**Real life examples:**

1.  **Make script executable:**
    Wrote `script.sh`. Want run.
    ```bash
    $ chmod u+x script.sh
    ```
    *Trans:* "To owner (u), add (+) execute (x)".

2.  **Protect secret doc:**
    No one read.
    ```bash
    $ chmod go-rwx secret.txt
    ```
    *Trans:* "Group/Others (go), remove (-) read, write, exec".

3.  **Share group, block rest:**
    ```bash
    $ chmod g=r,o= file.txt
    ```
    *Trans:* "Group read only. Others... nothing (empty)".

4.  **Public for all:**
    ```bash
    $ chmod a+r photo.jpg
    ```
    *Trans:* "All (a), add read".

Mode great for quick changes ("forgot exec permission").

@section: 4. `chmod`: The Mathematician (Octal Mode)

Symbolic slow if define all permissions at once. Pros use **Octal Mode** (numeric).
Linux goes "Matrix".

Each permission numeric value binary bits:

*   **`r` (Read) = 4**
*   **`w` (Write) = 2**
*   **`x` (Exec) = 1**
*   **`-` (None) = 0**

### Magic Sum
To know group permission, **SUM** values.

*   Want Read Write (`rw-`): 4 + 2 = **6**.
*   Want Read Exec (`r-x`): 4 + 1 = **5**.
*   Want All (`rwx`): 4 + 2 + 1 = **7**.
*   Want Read Only (`r--`): 4.
*   Want Nothing (`---`): 0.

### Three Digit Code
3 groups (Owner, Group, Others), 3 numbers.

**Legendary `755`:**
Standard program/public folder.
*   Owner (7): 4+2+1 = `rwx` (All).
*   Group (5): 4+0+1 = `r-x` (Read Exec).
*   Others (5): 4+0+1 = `r-x` (Read Exec).
*   *Result:* `-rwxr-xr-x`.

**Private `600`:**
Ideal secret text files (SSH keys).
*   Owner (6): 4+2 = `rw-` (Read Write).
*   Group (0): None.
*   Others (0): None.
*   *Result:* `-rw-------`.

**Deadly Sin `777`:**
*   Owner (7): `rwx`.
*   Group (7): `rwx`.
*   Others (7): `rwx`.
*   *Result:* `-rwxrwxrwx`.
*   *Meaning:* **ANYONE** on system read, delete, mod, run file.
*   *Usage:* **NEVER**. Internet tutorial says "chmod 777 fix problem", tutorial trash. Opening house door removing locks.

**Octal command examples:**
```bash
$ chmod 755 script.sh
$ chmod 644 doc.txt
$ chmod 700 private_folder/
```

**Trick:** `-R` (Recursive).
Change folder and *everything inside*:
```bash
$ chmod -R 755 /var/www/html
```

@quiz: What octal numeric equivalent permissions `rw-r--r--`?
@option: 755
@correct: 644
@option: 600
@option: 777

@section: 5. `chown` & `chgrp`: Changing Owner

Sometimes problem not permissions, but **who** owns.
Default create file, yours. Sometimes give file other user (or `root`, or `www-data`).

### Command `chown` (Change Owner)
Only `root` (or `sudo`) can gift files. Can't "gift" file to user security (could fill disk trash not deletable... complex, trust me).

**Syntax:**
`chown [USER]:[GROUP] file`

**Examples:**

1.  **Change owner only:**
    ```bash
    $ sudo chown mary report.txt
    ```
    File belongs Mary. Group original.

2.  **Change owner and group (Common):**
    ```bash
    $ sudo chown www-data:www-data index.html
    ```
    Assigns user `www-data` and group `www-data`.

3.  **Change group only:**
    Use `chown :group file` (colon front) or `chgrp`.
    ```bash
    $ sudo chown :developers code.py
    # Or
    $ sudo chgrp developers code.py
    ```

4.  **Recursive (Careful!):**
    ```bash
    $ sudo chown -R john:john /home/john/
    ```
    Ensures everything John folder belongs John.

@section: 6. Dark Arts: Special Permissions

Seen 9 standard bits. Exist **3 special bits** grant superpowers. Advanced tools, recognize them.

Represented 4th digit octal (e.g. `4755`) or special letters `ls -l`.

### 6.1 SUID (Set User ID) - King's Mantle
*   **Value:** 4000.
*   **Symbol:** `s` instead `x` Owner (`rwsr-xr-x`).
*   **Does:** Run SUID file, **runs with permissions of FILE OWNER**, not yours.

**Classic example: `passwd`**
You (normal) need change password. Write `/etc/shadow`. But `/etc/shadow` owned `root` you no write.
How?
Program `/usr/bin/passwd` SUID bit belonging `root`.
Run `passwd`, program "disguises" `root` temporarily, edits file, exits.
Controlled privilege elevation.

### 6.2 SGID (Set Group ID) - Teamwork
*   **Value:** 2000.
*   **Symbol:** `s` in `x` Group (`rwxr-sr-x`).
*   **Does (folders):** Any file created inside **inherits FOLDER GROUP**, not user primary group.
*   **Use:** Collaboration. Shared folder `/srv/project` group `devs`, SGID on. Ana creates file, file belongs group `devs` (Peter edits) instead group `ana` (Peter cant touch).

### 6.3 Sticky Bit - Property Tag
*   **Value:** 1000.
*   **Symbol:** `t` in `x` Others (`rwxrwxrwt`).
*   **Does:** Folder where *all* write (like `/tmp`), prevents users deleting files **not theirs**.
*   **Use:** Look `/tmp`. Everyone dumps stuff. But can't delete other user temp files. Only owner (and root) delete own trash.

@quiz: What effect 'Sticky Bit' on directory like `/tmp`?
@option: Files delete auto after time.
@option: Anyone delete anything.
@correct: Prevents users deleting files not theirs, even with write permission on folder.
@option: Files stay RAM.

@section: 7. Default Mold: `umask`

Create file (`touch new`), permissions default? Why `644` not `600` `777`?

Decided by **`umask`** (User Mask).
Umask subtraction filter.
*   Files born trying `666` (rw-rw-rw-).
*   Folders born trying `777` (rwxrwxrwx).

Umask subtracts.
Typical `0022`.
*   File: 666 - 022 = **644** (rw-r--r--).
*   Folder: 777 - 022 = **755** (rwxr-xr-x).

Want privacy default, change umask `0077` (subtract all group/others).
*   File: 666 - 077 = **600** (rw-------). No one reads new files.

@section: 8. Practical Lab: Security Incident

Simulate audit scenario.

1.  **Create scenario:**
    ```bash
    $ mkdir /tmp/lab_perms
    $ cd /tmp/lab_perms
    $ touch secret.txt
    ```

2.  **Analysis:**
    `ls -l`.
    Probably `-rw-r--r--`. Everyone reads secret. Bad!

3.  **Shielding:**
    Make private.
    ```bash
    $ chmod 600 secret.txt
    $ ls -l
    -rw------- ... secret.txt
    ```
    Only you read.

4.  **Create public script:**
    ```bash
    $ echo "echo Hello" > script.sh
    ```
    Run: `./script.sh`.
    Error: `Permission denied`.
    Why? Missing `x`.

5.  **Make executable:**
    ```bash
    $ chmod u+x script.sh
    $ ./script.sh
    Hello
    ```
    Works!

6.  **Play groups:**
    *(Needs sudo)*
    Gift secret to root.
    ```bash
    $ sudo chown root:root secret.txt
    ```
    Try read: `cat secret.txt`.
    `Permission denied`.
    Not yours! Even created it, gave root, root has `rw-------`. Locked self out. Linux strict.

7.  **Clean:**
    ```bash
    $ cd ..
    $ rm -rf lab_perms
    ```

@section: Summary / Cheat Sheet

| Action | Command | Example |
| :--- | :--- | :--- |
| **See Perms** | `ls -l` | |
| **Numeric** | **r=4, w=2, x=1** | |
| **Exec** | `chmod +x` | `chmod +x run.sh` |
| **Private** | `chmod 600` | Only me rw. |
| **Standard File** | `chmod 644` | Me w, all r. |
| **Standard Folder** | `chmod 755` | Me w, all enter. |
| **Change Owner** | `chown user` | `sudo chown ana file` |
| **Change Group** | `chown :group` | `sudo chown :devs file` |
| **Recursive** | `-R` | `chmod -R 755 folder` |

Mastering permissions difference secure system colander. If "Permission Denied", don't run `sudo` `chmod 777`. Stop, `ls -l`, think who are you, what missing. **Be guardian, not intruder.**