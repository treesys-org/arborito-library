@title: Automation with Bash Scripting: The Power to Create
@icon: 📜
@description: The definitive guide (+500 lines) to stopping being a passive user and becoming a tool creator. Variables, loops, conditional logic, and how to automate your life.
@order: 5

# The Force Awakens: Your First Step into Programming

Welcome to the final lesson of "Linux Jr".

So far, you used Linux operator.
*   List files (`ls`).
*   Move (`mv`).
*   Install (`apt`).

User giving loose orders. But backup photos, compress date today, send remote, delete temp, Friday 3 AM?
Wake 3 AM type 4 commands? **No.**

Enter **Scripting**.
Script text file write commands execute sequential. Magic: **Logic**.
*"If folder exists, copy. If not, create"*. *"Repeat every file"*.

Massive guide, **Bash** language. Native terminal. No Python C++. Have it. Server glue.

Prepare. Stop using tools. Start building.

@section: 1. Initiation Ritual: Shebang Perms

Create script, text editor (`nano`).
Classic "Hello World", understand atoms.

### 1.1 Shebang (`#!`)
Terminal create:
```bash
$ nano hello.sh
```

Write:
```bash
#!/bin/bash
echo "Hello, automation world!"
```

First line sacred. **Shebang**.
*   `#`: Bash comment (ignore).
*   `!`: Followed exclam, Kernel directive.
*   `/bin/bash`: Tell system: *"Hey, file not normal text. Load `/bin/bash` pass file run"*.

Could be `#!/usr/bin/python3` Python. Today Bash.

### 1.2 Failed Exec
Save (`Ctrl+O`) exit (`Ctrl+X`).
Run:
```bash
$ ./hello.sh
bash: ./hello.sh: Permission denied
```
**Why?**
Linux security **never** execute text created. Download virus text `virus.txt` double click. Disaster.
File "Program", grant life (exec bit).

### 1.3 Activation (`chmod`)
```bash
$ chmod +x hello.sh
```
*   `+x`: Add eXecutable permission.

Now:
```bash
$ ./hello.sh
Hello, automation world!
```

**Why `./`?**
Write `hello.sh`, terminal search system folders (`/usr/bin`) not find.
Dot `.` "Right here". *"Run hello.sh current folder"*.

@section: 2. Variables: Boxes Labels

Algebra `x = 5`. Programming same.
Variable box name save data.

### 2.1 Creation (Assign)
Golden rule: **NO SPACES AROUND EQUAL.**

```bash
# BAD (Bash thinks "NAME" command "=" arg)
NAME = John

# GOOD
NAME="John"
AGE=25
DATE="2025-01-01"
```

### 2.2 Use (Interpolation)
See inside box, dollar `$`.

```bash
echo "Hi, I am $NAME age $AGE."
```

Precise (glue letter), braces `${}`:
```bash
FILE="photo"
echo "Process ${FILE}_vacation.jpg"
# No braces, search $FILE_vacation, not exist.
```

### 2.3 Quotes: War " vs '
Confuse beginners.

*   **Double (`"`):** "Soft". Magic inside. `$VAR` sub value.
*   **Single (`'`):** "Hard" literal. Protect inside. Nothing changes.

**Example:**
```bash
NAME="Ana"
echo "Hi $NAME"  -> Print: Hi Ana
echo 'Hi $NAME'  -> Print: Hi $NAME
```
*Tip:* Double default. Single symbols ($ ! ` \).

@section: 3. Interactivity: Talking Human (`read`)

Talk script boring. Listen.
`read` pause wait user type.

```bash
#!/bin/bash

echo "Name traveler?"
read USERNAME

echo "Welcome, $USERNAME. Engines..."
sleep 2  # Drama pause
echo "Ready!"
```

**Pro (`-p`):**
Question inside.
```bash
read -p "Age: " AGE
echo "$AGE years."
```

@section: 4. Arguments: Auto No Questions

Automate 4 AM, no human `read`.
Pass data start.
`./copy.sh file1.txt file2.txt`

Bash numbers words:

*   **`$0`**: Script name (`./copy.sh`).
*   **`$1`**: Arg 1 (`file1.txt`).
*   **`$2`**: Arg 2 (`file2.txt`).
*   ...
*   **`$#`**: Total args.

**Prac:**
`formal.sh`:
```bash
#!/bin/bash
echo "Morning Mr. $1."
echo "Surname $2."
```

Run:
```bash
$ ./formal.sh James Bond
Morning Mr. James.
Surname Bond.
```

@section: 5. Logic Power: Conditionals (`if`)

Decisions. A then B. Else C.

Syntax peculiar. Spaces strict.

```bash
if [ CONDITION ]; then
    # True
else
    # False
fi
```
*(`if` end `fi` reverse).*

### 5.1 Compare Numbers
*   `-eq`: Equal.
*   `-ne`: Not equal.
*   `-gt`: Greater Than.
*   `-lt`: Less Than.
*   `-ge`: Greater equal.
*   `-le`: Less equal.

**Example:**
```bash
read -p "Number: " NUM

if [ "$NUM" -gt 10 ]; then
    echo "Big."
else
    echo "Small."
fi
```
**IMP:** Spaces brackets `[ ... ]`.
`["$NUM" -gt 10]` -> **ERROR**.
`[ "$NUM" -gt 10 ]` -> **GOOD**.

### 5.2 Compare Text
*   `=`: Equal.
*   `!=`: Diff.
*   `-z`: Empty (Zero).

```bash
if [ "$USER" = "root" ]; then
    echo "Careful Admin."
fi
```

### 5.3 Check Files (Most useful)
90% admin.
*   `-f file`: Exist normal?
*   `-d file`: Exist dir?
*   `-e file`: Exist?
*   `-r file`: Read perm?
*   `-w file`: Write perm?

**"Safe Installer":**
```bash
CONFIG="/etc/myapp"

if [ -d "$CONFIG" ]; then
    echo "Exists. Nothing."
else
    echo "Creating..."
    mkdir "$CONFIG"
fi
```

@quiz: Operator `if` check num `$A` less `$B`?
@option: <
@option: -min
@correct: -lt
@option: <<

@section: 6. Repetition Force: Loops (`for`)

Convert 1000 png jpg. 1000 commands?
Loop `for` *"Each element list, do"*.

**Syntax:**
```bash
for VAR in LIST; do
    # Cmds
done
```

### 6.1 Manual List
```bash
for COLOR in red green blue; do
    echo "Like $COLOR"
done
```

### 6.2 Files (Globbing)
Powerful. Bash expand `*.txt`.

```bash
# Backup all txt
for FILE in *.txt; do
    echo "Copying $FILE..."
    cp "$FILE" "$FILE.bak"
done
```

### 6.3 Range
```bash
# Count 1 10
for i in {1..10}; do
    echo "Count: $i"
    sleep 1
done
```

@section: 7. Math Subshells

### 7.1 Math (`$(( ))`)
Integers only. Double paren.

```bash
A=5
B=2
SUM=$((A + B))
echo "$SUM"
```

### 7.2 Command Sub (`$( )`)
Vital. Save **result command** variable.
`$(cmd)`.

**Example: Date filename**
```bash
TODAY=$(date +%F)
# 2025-01-20

tar -czf "backup_$TODAY.tar.gz" /home/user/docs
```
Creates diff file day.

**Example: Who?**
```bash
ME=$(whoami)
echo "Running as: $ME"
```

@section: 8. Exit Codes: Success Traffic Light

Program end, secret number **Exit Code**.
*   **0**: Success.
*   **1-255**: Error.

Variable **`$?`**.

```bash
ls /no_exist
echo "Code: $?"
# Print: Code: 2
```

**Why?**
Logic chain.
"Try download. IF (&&) good, unzip. ELSE (||) error".

### Operators `&&` `||`
*   `cmd1 && cmd2`: Run 2 if 1 OK (0).
*   `cmd1 || cmd2`: Run 2 if 1 FAIL.

**Pro:**
```bash
# Update single line
sudo apt update && sudo apt upgrade -y
```
*(If `update` fail, `upgrade` no run).*

@section: 9. Functions: DRY

Code reuse function.

```bash
function log_error {
    echo "[ERROR] - $(date) - $1" >> error.log
    echo "Error! See log."
}

# Use
mkdir /root/test 2> /dev/null

if [ $? -ne 0 ]; then
    log_error "Cannot create root. Perm denied."
    exit 1
fi
```

@section: 10. Lab: "Download Organizer"

Real script.
**Prob:** Downloads chaos. Images, pdfs, zips.
**Sol:** Detect type move folders auto.

File `organizer.sh` `+x`.

```bash
#!/bin/bash

# Dir (Real user or $USER)
SRC="/home/$USER/Downloads"

# Check exist
if [ ! -d "$SRC" ]; then
    echo "Error: $SRC no exist."
    exit 1
fi

echo "Cleaning $SRC..."

# Enter
cd "$SRC"

# 1. Images
# Check jpg png exist
if ls *.jpg *.png >/dev/null 2>&1; then
    echo "Images found. Moving..."
    mkdir -p Images
    mv *.jpg *.png Images/
fi

# 2. Docs
if ls *.pdf *.docx *.txt >/dev/null 2>&1; then
    echo "Docs found. Moving..."
    mkdir -p Docs
    mv *.pdf *.docx *.txt Docs/
fi

# 3. Packages
if ls *.zip *.tar.gz *.deb >/dev/null 2>&1; then
    echo "Packages found. Moving..."
    mkdir -p Pkgs
    mv *.zip *.tar.gz *.deb Pkgs/
fi

echo "Clean!"
ls -F
```

**Analysis:**
1.  Vars paths.
2.  `if [ ! -d ]` check error (Defensive).
3.  `mkdir -p` safe.
4.  `>/dev/null` silence `ls` check.
5.  Auto organize.

@section: 11. Strict mode (`set`) and scheduling (cron/at)

### `set -euo pipefail`

```bash
set -e          # exit on first failing command
set -u          # error on undefined variables
set -o pipefail # pipeline fails if any stage fails
```

Often written once after the shebang: `set -euo pipefail`. Great for production; can break quick-and-dirty tests—use intentionally.

### cron and at

*   `crontab -e` — per-user recurring jobs (`minute hour dom mon dow command`).
*   `/etc/crontab`, `/etc/cron.*` — system jobs (root).
*   `at` — one-shot delayed execution.

Example: run organizer Sundays 03:00:

```bash
0 3 * * 0 /home/user/bin/organizer.sh >> /var/log/organizer.log 2>&1
```

**RHEL:** `cronie`; **Debian/Ubuntu:** `cron`. Check with `systemctl status cron` or `crond`.

### ShellCheck

Run `shellcheck script.sh` before production to catch quoting and portability issues.

@quiz: What does `set -u` do in Bash?
@option: Disable errors
@correct: Fail if an undefined variable is expanded
@option: Enable GUI mode

@quiz: Which file does a user edit for personal cron jobs?
@option: /etc/fstab
@correct: User crontab (`crontab -e`)
@option: /etc/hosts

@section: Summary / Cheat Sheet

| Sym | Meaning | Ex |
| :--- | :--- | :--- |
| `#!/bin/bash` | Shebang | 1st line. |
| `$VAR` | Var | `echo $NAME` |
| `read` | Input | `read -p "Say: " V` |
| `$1, $2` | Args | `./s.sh a b` |
| `$(cmd)` | Sub | `D=$(date)` |
| `$((A+B))` | Math | `S=$((2+2))` |
| `if [ ]; then` | Cond | Spaces `[ ]`. |
| `-eq, -gt` | Num | `-eq` equal. |
| `=, !=` | Text | `if [ "$A" = "y" ]` |
| `-f, -d` | File | `-f` file. |
| `&&` | AND | Run if OK. |
| `||` | OR | Run if FAIL. |
| `for X in Y` | Loop | `for f in *` |

Power. No boring tasks. Script, perm, machine work coffee.