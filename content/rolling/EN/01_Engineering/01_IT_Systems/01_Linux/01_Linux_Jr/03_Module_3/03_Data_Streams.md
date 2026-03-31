@title: Data Streams: Linux Plumbing (Pipes & Redirections)
@icon: 🚿
@description: The definitive guide (+500 lines) to understanding how data flows in Linux. Master stdin, stdout, stderr, pipes, redirections, and the art of chaining commands.
@order: 3

# Software Plumbing: Mastering Data Streams

Welcome to the lesson that will change how you see computing forever.

So far, you used commands isolated. Used `ls` see files. `cd` move. `cat` read.
Useful tools, but **isolated**. Like hammer, saw, screwdriver, used separately.

Real magic Linux, reason dominates servers supercomputing, resides simple revolutionary idea born 70s:

> **"Write programs that do one thing and do it well. Write programs to work together. Write programs to handle text streams, because that is a universal interface."**
> — *Doug McIlroy (Inventor of Unix Pipes)*

Linux commands not lonely islands. LEGO pieces. Connect command output to input another. Redirect what program "says" to file. Chain 20 commands process terabytes seconds.

In massive lesson, become **Digital Plumbers**. Learn connect pipes, divert data flows, filter info like water.

Prepare. Deep system guts.

@section: 1. Three Sacred Channels (Standard Streams)

Understand connecting, understand communicating.
Imagine program Linux (`ls`, `cat`, `grep`) small machine industrial robot.

Default, robots born **three hoses** connected. No more, no less. Hoses called **Standard Streams**, numbers assigned Kernel.

### 1.1 STDIN (Standard Input) - Channel 0
*   **Program Ear.**
*   Where receives info.
*   **Default:** Connected **Keyboard**.
*   Typing terminal, sending data STDIN running program.

### 1.2 STDOUT (Standard Output) - Channel 1
*   **Program Mouth.**
*   Where program "speaks" spits normal results.
*   **Default:** Connected **Screen** (Terminal).
*   `ls` shows list, sending text STDOUT monitor.

### 1.3 STDERR (Standard Error) - Channel 2
*   **Emergency Mouth.**
*   Separated special channel reserved exclusively screaming **errors**.
*   **Default:** Also connected **Screen**.
*   *Why separated STDOUT?* Vital. Saving program results file, don't want error messages mixing valid data. Want errors screen while data saves silently. Linux separates "wheat" (STDOUT) "chaff" (STDERR).

**Visual Summary:**
```text
      KEYBOARD (Input)
         |
         v
      [ STDIN (0) ]
         |
    +-----------+
    |  PROCESS  |
    +-----------+
      |       |
      |       +-----> [ STDERR (2) ] ----> SCREEN (Errors)
      |
      +-------------> [ STDOUT (1) ] ----> SCREEN (Data)
```

Admin mission disconnect hoses default places (keyboard/screen) connect where want (files/programs). **Redirecting**.

@section: 2. Output Redirection: Saving the Flow

Imagine command generating lots info, `ls -R /` (list all files). Run it, screen fills text, lost.
Want save text file read later.

### 2.1 Greater Than Operator (`>`) - Overwrite
Symbol `>` disconnects STDOUT screen plugs file.

**Syntax:**
`command > file`

**Example:**
```bash
$ echo "Hello world" > greeting.txt
```
If do:
1.  `echo` generates "Hello world".
2.  Instead screen, goes `greeting.txt`.
3.  `cat greeting.txt`, see text.

**DESTRUCTION DANGER!**
Operator `>` destructive.
If `greeting.txt` existed had life novel inside, **deleted instantly** replaced "Hello world". No "undo". Careful.

### 2.2 Double Greater Than (`>>`) - Append
Add info end file without deleting, use `>>`.

**Syntax:**
`command >> file`

**Example:**
```bash
$ echo "First line" > diary.txt
$ echo "Second line" >> diary.txt
$ echo "Third line" >> diary.txt
```
Read file (`cat diary.txt`), see three lines.
Operator `>>` safe. File not exist, creates. Exists, writes end.

**Real Use Case:**
Log script execution.
```bash
$ date >> backup_log.log
```

@section: 3. Input Redirection: Feeding the Robot

Opposite: disconnect keyboard tell program read data file.
Use Less Than Operator (`<`).

**Syntax:**
`command < file`

Many commands accept filenames arguments (`cat file.txt`), strict programs only read STDIN.

**Classic Example: `wc` (Word Count)**
Command `wc` counts lines, words, chars.
```bash
$ wc -l < diary.txt
3
```
Here, `wc` doesn't know file `diary.txt` exists. Only knows text arriving input hose (STDIN). Counts spits result.

**Advanced Example: Databases**
Restore MySQL DB:
```bash
$ mysql -u user -p database_name < backup.sql
```
"Injecting" content `.sql` directly DB engine.

@section: 4. Mystery Channel 2 (STDERR)

90% newbies confused.
Experiment. List existing file and non-existing.

```bash
$ ls exist.txt no_exist.txt > output.txt
ls: cannot access 'no_exist.txt': No such file or directory
```

**What happened?**
1.  Redirected output (`>`) file `output.txt`.
2.  However, error message (`ls: cannot access...`) **appeared screen**.
3.  Look file (`cat output.txt`), see `exist.txt` info, NO error.

**Explanation:**
Operator `>` only redirects **Channel 1 (STDOUT)**.
Error message travels **Channel 2 (STDERR)**, still connected screen. Hoses separate.

### 4.1 Redirecting Errors (`2>`)
Capture errors file, specify channel number:

```bash
$ ls exist.txt no_exist.txt 2> errors.log
```
Now:
*   Valid `exist.txt` screen (channel 1 not redirected).
*   Error message saves `errors.log` not screen.

### 4.2 Black Hole (`/dev/null`)
Sometimes errors irrelevant. Running script don't want screen full annoying warnings. Silence.
Linux special device `/dev/null`. **Digital black hole**. Everything sent disappears forever.

```bash
# Search files ignoring "Permission denied" errors
$ find / -name "secret" 2> /dev/null
```
Indispensable admin trick. Without `2> /dev/null`, `find /` fills screen trash trying enter system folders no permission. Sending black hole, only useful results.

### 4.3 Redirecting ALL (`&>` or `2>&1`)
Want good (STDOUT) bad (STDERR) same file (full install log).

**Modern (Bash):**
```bash
$ command &> all.log
```
`&` means "both channels".

**Classic (Compatible):**
```bash
$ command > all.log 2>&1
```
Hieroglyphic, read slow:
1.  `> all.log`: "Connect channel 1 (Out) to file".
2.  `2>&1`: "Connect channel 2 (Error) where channel 1 connected (&1)".
Splicing error hose into output hose.

@quiz: Running backup command (`backup.sh`) want save log what happened, but errors separate file `errors.txt`. Syntax?
@option: ./backup.sh > log.txt > errors.txt
@correct: ./backup.sh > log.txt 2> errors.txt
@option: ./backup.sh &> log.txt
@option: ./backup.sh | errors.txt

@section: 5. The Pipe (`|`)

Crown jewel. Invention made Unix great.
Vertical bar `|` (Pipe) allows connecting **Output (STDOUT)** command directly **Input (STDIN)** next command.

No intermediate files. No disk save. Data flows memory program program water pipe.

**Plumber Analogy:**
*   Cmd 1: Pump water well.
*   Cmd 2: Carbon filter cleans.
*   Cmd 3: Bottle store.

No pipes (clumsy):
1.  Pump bucket.
2.  Bucket filter pour.
3.  Filter bucket.
4.  Pour bucket bottle.

Pipes (Linux):
Connect Pump -> Filter -> Bottle. Water flows continuously.

**Syntax:**
`cmd1 | cmd2 | cmd3`

### Basic Example
Count files `/etc`.
1.  `ls /etc`: Lists files (generates lines).
2.  `wc -l`: Counts lines text arriving.

```bash
$ ls /etc | wc -l
245
```
`ls` didn't write screen. Passed list invisibly `wc`, `wc` counted lines showed number.

### Filter Philosophy
Pipes useful, need commands act **Filters**. Programs designed receive text, modify, spit modified.

See powerful filters.

@section: 6. Filter Arsenal

Commands living right of pipes. Vital learn.

### 6.1 `grep` (Searcher)
Seen it, king pipes. Filter lines. Pass only containing search.

```bash
# List processes -> Filter only python
$ ps aux | grep python
```

### 6.2 `sort` (Sorter)
Sorts alpha/numeric lines received.

```bash
# List users, sort alpha
$ cut -d: -f1 /etc/passwd | sort
```

Options:
*   `-n`: Numeric (10 after 2).
*   `-r`: Reverse.
*   `-k`: Key (column).

### 6.3 `uniq` (Deduplicator)
Removes **consecutive** duplicate lines.
**NOTE!** Only works if duplicates adjacent. **Always** use after `sort`.

```bash
# Bad (duplicates separated fail)
$ command | uniq

# Good (sort joins equals, uniq removes)
$ command | sort | uniq
```
Option: `uniq -c` (Count). Tells how many times line appeared.

### 6.4 `head` & `tail` (Cut)
Take start or end flow.

```bash
# See 5 biggest files folder
# ls -lS (sort size) -> head -n 6 (header + top 5)
$ ls -lS | head -n 6
```

### 6.5 `tr` (Translate)
Substitute/delete chars. Quick cleaning.

```bash
# Uppercase
$ echo "hello world" | tr 'a-z' 'A-Z'
HELLO WORLD

# Delete spaces
$ echo "hello   world" | tr -d ' '
helloworld
```

### 6.6 `cut` (Vertical Cutter)
Have data table. `cut` extracts specific **column**.
Defines "delimiter" (char separating columns).

Example: `/etc/passwd` uses `:` separator. User col 1.
```bash
$ cat /etc/passwd | cut -d: -f1
```
*   `-d:` Delimiter.
*   `-f1`: Field 1.

### 6.7 `tee` (T-Junction)
Want save flow file, **also** see screen keep processing.
Redirection `>` blind (saves, no see).
`tee` T-piece. Sends flow file AND pass screen (or pipe).

```bash
# List, save file, count lines
$ ls -l | tee list.txt | wc -l
```
Two things:
1.  `list.txt` created content `ls`.
2.  Screen shows number lines.

@section: 7. `xargs`: Magic Bridge

Sometimes use result command **arguments** next command, not input text.
Complex start.

Example: `find` returns list filenames. Want delete `rm`.
*   `find . -name "*.tmp" | rm` -> **FAILS.**
    *   Why? `rm` doesn't read filenames STDIN. `rm` expects arguments (`rm file1 file2`). `rm` ignores text pipe.

Need **`xargs`**.
`xargs` takes text pipe converts arguments next command.

```bash
# Correct
$ find . -name "*.tmp" | xargs rm
```
`xargs` builds command: `rm file1.tmp file2.tmp ...`

**Space danger:**
Files spaces ("my file.tmp"), `xargs` fail thinks two files ("my", "file.tmp").
Fix `print0`:
```bash
$ find . -name "*.tmp" -print0 | xargs -0 rm
```
Uses null invisible char separate, bulletproof.

@section: 8. Practical Lab: Log Analysis

Real forensic case pipes.
Web server, want know attacking IPs.

**Scenario:** File `access.log` thousands lines:
`192.168.1.50 - - [20/Oct/2023...] "GET /index.html..." 200 ...`

**Goal:** "Top 5" IPs visiting.

**Step 1: Extract IPs**
IPs col 1. Space separator.
```bash
$ cat access.log | cut -d' ' -f1
```
*Result:* Long list unordered IPs.

**Step 2: Sort**
Count duplicates, sort first.
```bash
$ cat access.log | cut -d' ' -f1 | sort
```
*Result:* Equal IPs together.

**Step 3: Count occurrences**
Use `uniq -c`.
```bash
$ cat access.log | cut -d' ' -f1 | sort | uniq -c
```
*Result:* Lines `  45 192.168.1.50` (45 visits).

**Step 4: Sort quantity**
Highest number. Sort (`sort`), numeric (`-n`) reverse (`-r`).
```bash
$ cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr
```

**Step 5: Top 5**
Cut 5 lines.
```bash
$ cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr | head -n 5
```

Built complex statistical tool single line! Power Linux.

@quiz: What exactly does `sort | uniq -c`?
@option: Deletes duplicate files disk.
@option: Sorts lines deletes identical no count.
@correct: Sorts lines group, counts appearances, removes visual redundancy.
@option: Counts unique words text.

@section: Summary / Cheat Sheet

| Symbol | Name | Action |
| :--- | :--- | :--- |
| `>` | Redir Out | STDOUT file (Overwrite). |
| `>>` | Append Out | STDOUT file (Append). |
| `2>` | Redir Error | STDERR file. |
| `&>` | Redir Total | STDOUT+STDERR file. |
| `<` | Redir In | File to STDIN. |
| `|` | Pipe | Out A -> In B. |
| `/dev/null`| Black Hole | Discard data. |

**Essential Filters:**
*   `grep`: Search text.
*   `sort`: Sort lines.
*   `uniq`: Remove/count dups.
*   `cut`: Extract cols.
*   `wc`: Count.
*   `tee`: Save & show.
*   `tr`: Transform.
*   `xargs`: Flow to args.

Possess alchemy skill data. No Excel logs. No expensive programs. Terminal.