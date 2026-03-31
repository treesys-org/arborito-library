@title: Archiving and Compression: The Master Guide (tar, gzip, zip)
@icon: 📦
@description: The definitive guide (+500 lines) on how to package and compress data in Linux. Master 'tar', understand difference archiving/compression, handle gzip, bzip2, xz, zip.
@order: 5

# The Art of Packing: Archiving and Compression

Welcome to digital logistics.

Move house. 500 books, 2000 photos, 50 pairs socks.
Truck?
Throw socks one by one? No. Chaos. Lose half, hours.

Do:
1.  **Group:** Boxes. 50 socks "1 box".
2.  **Compress:** Winter clothes, vacuum bags space.

Computing identical.
Web server thousands small config, images, scripts. Send server backup, can't send 50,000 files loose. Network saturate headers.

Need **Archive** (box) **Compress** (vacuum).

Windows spoiled. Right click "Send compressed (zip)", system does both silent.
Linux (Unix), tasks **separate operations** **separate tools**.

Massive guide, dissect oldest venerated tool: **`tar`**. Why `.tar` no compress, extensions `.tar.gz` `.tar.bz2` `.tar.xz`, handle `.zip` Windows colleagues.

Prepare. Pack world.

@section: 1. Fundamental Concepts: Suitcase and Vacuum Bag

Before commands, vital difference technical.

### 1.1 Archiving
**Goal:** Join files folders single container.
**Result:** One big file.
**Size:** Sum exact size originals (+ metadata). **No space saving.**
**Tool:** `tar`.

10 photos 1MB:
*   Archive -> 1 file 10MB.

### 1.2 Compression
**Goal:** Reduce size math (algorithms) remove redundancy.
**Result:** File unreadable smaller.
**Tool:** `gzip`, `bzip2`, `xz`.

1 text file 10MB repeated words:
*   Compress -> 1 file 2MB.

### Unix Philosophy (Do one thing well)
Unix compression (`gzip`) only: compress **ONE** file. `gzip` no folders. Compress folder `gzip`, error.

Workflow Linux two steps (often together):
1.  `tar` glue files continuous roll (`.tar`).
2.  `gzip` compress roll (`.tar.gz`).

@section: 2. `tar`: Tape Archiver

`tar` **T**ape **AR**chive.
Born 70s, backups magnetic tapes. `tar` read disk write sequential tape.
No tapes today, use `tar` same: write files sequential "container file".

### Sacred Syntax
`tar` famous options. Memorize **"Three Functions"** **"Two Modifiers"**.

**3 Functions (What do):**
Choose one.
1.  `-c`: **C**reate (New).
2.  `-x`: e**X**tract (Unpack).
3.  `-t`: lis**T** (Index without extracting).

**2 Modifiers (Almost always):**
1.  `-f`: **F**ile. Work file, not physical tape. **IMPORTANT:** `f` always end options, before filename.
2.  `-v`: **V**erbose. Show screen filenames processing. Silent otherwise.

@section: 3. Creating Archives (.tar)

Create first archive.
Folder `project` code.

**Command:**
```bash
$ tar -cvf project.tar project/
```
*   `-c`: Create.
*   `-v`: Progress.
*   `-f`: Filename next.

**Output:**
```text
project/
project/main.c
project/header.h
project/logo.png
...
```
`ls -lh` see `project.tar`.
Size sum originals. No compress, package.

**ORDER WARNING!**
`tar -cvf dest source`.
Reverse (`tar -cvf source dest`), **destroy original data** overwrite empty tar. `tar` old no ask. Careful.

@section: 4. Compressing on the fly

Have box (`.tar`), remove air.
Two steps:
1.  `tar -cvf project.tar project/`
2.  `gzip project.tar` (Creates `project.tar.gz` deletes `.tar`).

`tar` modern allows "plugins" compression adding letter flag.

### 4.1 Standard: GZIP (`-z`)
*   **Ext:** `.tar.gz` or `.tgz`.
*   **Speed:** Fast.
*   **Comp:** Decent.
*   **Use:** 95% times. Internet standard.

**Create:**
```bash
$ tar -czvf project.tar.gz project/
```
*(Note extra `z`).*

### 4.2 Powerful: XZ (`-J`)
*   **Ext:** `.tar.xz`.
*   **Speed:** Slow compress, fast decompress.
*   **Comp:** Extreme. Smaller gzip.
*   **Use:** Distribute software (Kernel), backups long term space critical.

**Create:**
```bash
$ tar -cJvf project.tar.xz project/
```
*(Note capital `J`).*

### 4.3 Old: BZIP2 (`-j`)
*   **Ext:** `.tar.bz2`.
*   **Use:** Popular 2000s. Better gzip worse xz. Losing popularity, seen old systems.

**Create:**
```bash
$ tar -cjvf project.tar.bz2 project/
```
*(Note lower `j`).*

@quiz: 10GB log folder legal audit 5 years. Space minimum time irrelevant. Algorithm?
@option: gzip (-z)
@option: bzip2 (-j)
@correct: xz (-J)
@option: zip

@section: 5. Extracting Files

Downloaded `program.tar.gz`. Open?

Function **`-x`** (eXtract).

### Smart Decompression
Modern `tar` (GNU tar), **no need tell compression**. `tar` smart. Detects gzip, bzip2, xz auto.

Universal command:

```bash
$ tar -xvf any_file.tar.gz
$ tar -xvf any_file.tar.bz2
$ tar -xvf any_file.tar.xz
```
*   `-x`: Extract.
*   `-v`: Verbose.
*   `-f`: File.

### Tarbomb Danger
Evil/careless create tar without folder container.
Download `photos.tar.gz`.
Expect folder `photos/` inside.
Run... BAM!
Current folder (Desktop/Home) fills 10,000 loose images `img001.jpg`... mix files. Nightmare.

**Prevent:**
Before extract, **LOOK**.

@section: 6. Looking without Touching (`-t`)

Function **`-t`** (lisT) view index. Ingredients label.

```bash
$ tar -tvf suspicious.tar.gz
```

Safe (Good):
```text
drwxr-xr-x user/user 0 2023-01-01 container_folder/
-rw-r--r-- user/user 10 2023-01-01 container_folder/photo1.jpg
...
```
Inside `container_folder/`. Safe.

Tarbomb (Bad):
```text
-rw-r--r-- user/user 10 2023-01-01 photo1.jpg
-rw-r--r-- user/user 10 2023-01-01 photo2.jpg
...
```
Loose root. **Danger!**

### Defuse Tarbomb
Create containment folder.

```bash
$ mkdir safe_zone
$ tar -xvf bomb.tar.gz -C safe_zone/
```
Option **`-C`** (Change directory) tells `tar`: "Change folder before extract". Shield.

@quiz: Download `unknown.tar.gz`. First command safety?
@option: tar -xvf unknown.tar.gz
@correct: tar -tvf unknown.tar.gz
@option: tar -czvf unknown.tar.gz
@option: rm unknown.tar.gz

@section: 7. ZIP World: Windows Living

Send files Windows user. `.tar.gz`, ask what is it fail open. Windows `.zip` native.

Linux tools: `zip` `unzip`. (`sudo apt install zip unzip`).

### Compressing ZIP
Syntax: `zip [options] dest.zip source`

**Important:** `zip` not recursive default. Zip folder no options, empty folder.
Always **`-r`** (Recursive).

```bash
# Correct
$ zip -r work.zip work_folder/
```

### Decompressing ZIP
Syntax: `unzip file.zip`

```bash
$ unzip work.zip
```

**List content:**
```bash
$ unzip -l work.zip
```

**Why not ZIP Linux?**
ZIP old, disadvantage Unix: **Not preserve permissions (owner, exec bit)** reliably.
`tar` designed preserve metadata, permissions, owners, dates. System backups software transfer `tar`. Photos aunt `zip`.

@section: 8. Advanced Use Cases

### 8.1 Excluding Files
Backup project, exclude `node_modules` (200MB trash) `.git`.

Use `--exclude`.

```bash
$ tar -czvf backup_web.tar.gz --exclude='node_modules' --exclude='.git' my_project/
```
*Note: Pattern exclude no full path, name.*

### 8.2 Extracting single file
Backup 50GB (`backup.tar.gz`) recover one deleted file (`photos/dog.jpg`). Don't unzip 50GB.

Add filename end command.

```bash
$ tar -xvf backup.tar.gz photos/dog.jpg
```
`tar` search extract stop.

@section: 9. Practical Lab: Rescue Op

Practice terminal.

1.  **Prep:**
    `/tmp` test env.
    ```bash
    $ cd /tmp
    $ mkdir lab_tar
    $ cd lab_tar
    $ mkdir docs
    $ touch docs/info{1..100}.txt
    ```
    (100 text files).

2.  **Archive:**
    Tar simple.
    ```bash
    $ tar -cvf docs.tar docs/
    $ ls -lh
    ```

3.  **Compress Gzip:**
    ```bash
    $ tar -czvf docs.tar.gz docs/
    $ ls -lh
    ```

4.  **Disaster:**
    Delete original.
    ```bash
    $ rm -rf docs
    ```
    Lost data!

5.  **Inspect:**
    Check backup.
    ```bash
    $ tar -tvf docs.tar.gz
    ```

6.  **Recover:**
    Restore.
    ```bash
    $ tar -xvf docs.tar.gz
    $ ls -R
    ```
    100 files back!

@section: Summary / Cheat Sheet

| Action | Command | Notes |
| :--- | :--- | :--- |
| **Create .tar.gz** | `tar -czvf file.tar.gz folder/` | Standard, fast. |
| **Create .tar.xz** | `tar -cJvf file.tar.xz folder/` | Best comp, slow. |
| **Extract (Any)** | `tar -xvf file.tar.gz` | Detects auto. |
| **List content** | `tar -tvf file.tar.gz` | View no touch. |
| **Extract folder** | `tar -xvf file.tar -C dest/` | Avoid mess. |
| **Create ZIP** | `zip -r file.zip folder/` | Windows. Use `-r`. |
| **Extract ZIP** | `unzip file.zip` | |

**Mnemonics:**
*   **c**reate
*   e**x**tract
*   **v**erbose
*   **z** (**gz**ip)
*   **j** (bzip2)
*   **f** (**f**ile - always end flags)

Now move mountains data pocket. Logistics master.