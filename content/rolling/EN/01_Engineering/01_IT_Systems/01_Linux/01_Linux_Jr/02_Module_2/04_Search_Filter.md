@title: Search and Filters: The Digital Detective (find, grep)
@icon: 🕵️‍♂️
@description: The definitive guide (+500 lines) to finding any lost file or hidden text in your system. Master 'find', 'grep', 'locate', and basic regular expressions.
@order: 4

# The Digital Detective: How to Find a Needle in a Terabyte Haystack

Welcome to Module 2, Lesson 4.

So far, we've assumed you know where your files are. You do `cd Documents` because you know they are there. But what happens when you **don't know**?

Imagine this scenario:
*   You are a server administrator.
*   The hard drive has mysteriously filled up to 100%.
*   You don't know which file is taking up the space.
*   You don't know where it is.
*   And to top it off, your boss asks you to find "that config file that contained the word 'password' we created 3 years ago".

If you try to search for this by opening folders one by one (`cd`, `ls`, `cd`, `ls`...), it will take a lifetime. A modern Linux system has hundreds of thousands of files.

You need **forensic search** tools.
In this massive lesson, we are going to turn you into a digital bloodhound. You will learn to locate files by name, size, creation date, or even by the text they contain inside.

This guide is divided into three fundamental parts:
1.  **Fast Search:** `locate` and Wildcards.
2.  **Deep Search:** The all-powerful `find`.
3.  **Content Search:** The legendary `grep`.

Get ready. We're going to dig.

@section: Part 1: Wildcards (Globbing)

Before using search commands, you must understand how the Shell (Bash) helps you refer to groups of files. This is called "Globbing".
This is not a command; it is a feature of the terminal itself that works with *any* command (`ls`, `rm`, `cp`).

### The Asterisk (`*`)
The asterisk is the ultimate wildcard. It means: **"Anything, of any length"**.

*   `*.jpg`: Everything ending in .jpg.
*   `photo*`: Everything starting with photo.
*   `*vacation*`: Everything containing the word "vacation" in the middle, beginning, or end.
*   `*`: Everything. Absolutely everything.

**Practical Example:**
```bash
# List all PNG files
$ ls *.png

# Delete all files starting with 'draft'
$ rm draft*
```

### The Question Mark (`?`)
It is more precise than the asterisk. It means: **"Any character, but ONLY ONE"**.

Imagine you have: `photo1.jpg`, `photo2.jpg`, `photo10.jpg`.
*   `ls photo*.jpg`: Shows all three (because `*` accepts any length).
*   `ls photo?.jpg`: Shows only `photo1.jpg` and `photo2.jpg`. DOES NOT show `photo10.jpg` because "10" is two characters, and `?` only stands for one.

### The Brackets (`[]`)
This is for snipers. It means: **"One of the characters inside"**.

*   `ls photo[123].jpg`: Searches `photo1.jpg`, `photo2.jpg`, or `photo3.jpg`. Does not search `photo4.jpg`.
*   **Ranges:** You can use hyphens.
    *   `[0-9]`: Any number from 0 to 9.
    *   `[a-z]`: Any lowercase letter.
    *   `[A-Z]`: Any uppercase letter.

**Precision Example:**
I want to list files that are `data_a.txt` or `data_b.txt`, but not `data_c.txt`.
```bash
$ ls data_[ab].txt
```

### The Braces (`{}`) - Expansion
Although technically not "globbing" but "expansion", it is used similarly. It serves to generate sequences.
```bash
# Create folders Year_2023, Year_2024, Year_2025
$ mkdir Year_{2023..2025}

# List files with two different extensions at once
$ ls *.{jpg,png}
```

@quiz: You have files `img1.png`, `img2.png`, `img10.png`, and `imgA.png`. If you execute `ls img[0-9].png`, which ones will be shown?
@option: All.
@option: img1.png, img2.png, and img10.png.
@correct: Only img1.png and img2.png.
@option: Only img10.png.

@section: Part 2: `locate` (The Fast Index)

Sometimes you know the file name and want it NOW. You don't want to wait for the computer to scan the hard drive.
That's what `locate` is for.

### How does it work?
`locate` does not search your hard drive in real-time. That would be slow.
`locate` searches a **Database** (a giant index) that the system maintains with the list of all files.
It's like searching the index of a book instead of reading all the pages.

**Advantage:** It is instant. Takes milliseconds to find among millions of files.
**Disadvantage:** The index might be outdated. If you created a file 1 minute ago, `locate` won't find it because it's not in the index yet.

### Basic Usage
```bash
$ locate passports
/home/user/Documents/passports_scan.pdf
/home/user/travels/passports.txt
```

### Updating the Index (`updatedb`)
The index updates automatically once a day (usually at night).
If you just created a file and `locate` doesn't find it, you can force manual index update.
(Requires admin permissions).

```bash
$ sudo updatedb
```
Wait a few seconds and try `locate` again.

@section: Part 3: `find` (The Absolute Seeker)

If `locate` is fast but dumb, `find` is slow but **omnipotent**.
`find` traverses the real hard drive, file by file, in real-time. It uses no indexes. It is the most precise and powerful tool in the Linux arsenal.

`find` syntax is different from other commands and usually scares newbies. Let's demystify it.

### Anatomy of `find`
```bash
find [WHERE] [CRITERIA] [ACTION]
```
1.  **WHERE:** In which folder do I start searching? (If you put nothing, it searches current `.`).
2.  **CRITERIA:** What are you looking for? (Name, size, date...).
3.  **ACTION:** (Optional) What do I do when I find it? (Default: print to screen).


### 3.1 Search by Name

The most common criterion. We use `-name`.

```bash
# Search 'report.txt' in current folder and subfolders
$ find . -name "report.txt"

# Search in ENTIRE system (needs sudo if searching protected folders)
$ sudo find / -name "hosts"
```

**Case Sensitivity:**
`-name` distinguishes case. "Photo.jpg" is not "photo.jpg".
If you want to search ignoring case, use `-iname` (Insensitive Name).
```bash
$ find . -iname "PHOTO.JPG"
# Will find: Photo.jpg, photo.jpg, PHOTO.JPG...
```

**Using Wildcards:**
Whenever you use wildcards with `find`, **USE QUOTES**. Otherwise, the shell will try to expand them before `find` sees them and give an error.
```bash
# Incorrect (often fails)
$ find . -name *.jpg

# CORRECT
$ find . -name "*.jpg"
```


### 3.2 Search by Type

Sometimes a directory is named the same as a file. You can filter what type of object you seek with `-type`.
*   `f`: File (Normal file).
*   `d`: Directory (Folder).
*   `l`: Link (Symbolic link).

```bash
# Search folders named 'config'
$ find /etc -type d -name "config"

# Search files named 'python' (to avoid finding the python folder)
$ find /usr -type f -name "python"
```


### 3.3 Search by Size (Vital for disk cleaning)

This is `find`'s superpower. You can search files based on how much space they take up.
We use `-size`. Modifiers are:
*   `k`: Kilobytes.
*   `M`: Megabytes.
*   `G`: Gigabytes.
*   `+`: Greater than.
*   `-`: Less than.

**Real Examples:**
```bash
# Find giant files (more than 1 Gigabyte) in my home
# Useful for freeing space!
$ find ~ -size +1G

# Find empty files (0 bytes)
$ find . -size 0

# Find files weighing EXACTLY 10 Megs
$ find . -size 10M

# Find files between 100MB and 1GB (Combining criteria)
$ find . -size +100M -size -1G
```


### 3.4 Search by Time (The Forensic)

Remember you edited a file yesterday but don't know which one? `find` knows.
Linux keeps three dates for each file:
1.  **mtime (Modification Time):** When content changed.
2.  **atime (Access Time):** When last read/opened.
3.  **ctime (Change Time):** When metadata changed (permissions, name).

The unit is **days** (for hours use `-mmin`).
*   `-7`: Less than 7 days ago.
*   `+7`: More than 7 days ago.
*   `7`: Exactly 7 days ago.

**Examples:**
```bash
# Files modified in the last 24 hours (day 0)
$ find . -mtime -1

# Files modified more than 30 days ago (old)
$ find /var/log -mtime +30

# Files accessed less than 10 minutes ago (using minutes)
$ find . -amin -10
```


### 3.5 Search by User or Permissions

Useful for security auditors.

```bash
# Find files belonging to user 'john'
$ find /home -user john

# Find files with permissions 777 (dangerous, everyone can write)
$ find . -perm 777
```


### 3.6 Logical Operators (AND, OR, NOT)

You can combine all the above.
*   By default, if you put multiple criteria, it is an **AND** (all must be met).
*   `-o`: **OR** (one or the other met).
*   `-not` or `!`: **NOT** (inverts condition).

```bash
# Search .jpg OR .png files
$ find . -name "*.jpg" -o -name "*.png"

# Search files that are NOT from user root
$ find . -not -user root
```


### 3.7 The Action `-exec` (Danger and Power)

So far, `find` only lists files. But what if we want to *do* something with them?
For example: "Find all .jpgs and move them to Photos folder". Or "Find temp files and delete them".

We could do it by hand one by one, but `find` has the `-exec` option.
This option executes a command on every file it finds.

**Weird Syntax:**
`... -exec COMMAND {} \;`
*   `{}`: Is a placeholder. `find` substitutes this with the name of the found file.
*   `\;`: Indicates the command has finished. Escaping the semicolon is mandatory.

**Example: Mass Deletion (CAREFUL!)**
```bash
# Find .tmp files and delete them
$ find . -name "*.tmp" -exec rm {} \;
```
*Translation:* "For each .tmp file you find, execute `rm [file]`".

**Example: Change Permissions**
```bash
# Find all .sh scripts and make them executable
$ find . -name "*.sh" -exec chmod +x {} \;
```

**Safety Tip:**
Before running a destructive command with `-exec`, run `find` without `-exec` first to see what files it will find. Ensure the list is correct.

@quiz: You want to find all files in your current directory taking up more than 500 Megabytes. Which command do you use?
@option: find . -size 500
@option: locate +500M
@correct: find . -size +500M
@option: grep -size 500M

@section: Part 4: `grep` (Searching Inside Files)

`find` searches files by attributes (name, size).
`grep` searches **inside** file content. It is the ultimate text searcher.

Its name comes from **G**lobal **R**egular **E**xpression **P**rint.

### Basic Usage
```bash
grep "text_to_search" file
```

Example: Search if I have a user named "pepe" on the system.
```bash
$ grep "pepe" /etc/passwd
```
If nothing comes out, pepe doesn't exist. If a line comes out, pepe is there.

### Recursive Search (`-r`)
This is what you'll use 99% of the time. You want to search for a word in **all** files of a folder and its subfolders.

Imagine you are a programmer and want to know in which file of your project you used the function `calculate_vat`.
```bash
$ grep -r "calculate_vat" .
./src/main.py: def calculate_vat(price):
./src/utils.py: from main import calculate_vat
```
`grep` tells you the file and shows you the line.

### Vital `grep` Options

1.  **Ignore Case (`-i`):**
    Searches "error", "ERROR", "Error"...
    ```bash
    $ grep -i "error" log.txt
    ```

2.  **Line Number (`-n`):**
    Tells you on which line the text is. Essential for editing code.
    ```bash
    $ grep -n "TODO" main.c
    45: // TODO: Fix this bug
    ```

3.  **Invert Search (`-v`):**
    Shows all lines that do **NOT** contain the text.
    *Use case:* Viewing a config file without comments. (Comments in Linux usually start with `#`).
    ```bash
    $ grep -v "^#" /etc/ssh/sshd_config
    ```

4.  **Filename Only (`-l`):**
    If there are many matches, sometimes you don't want to see text, just know which files contain it.
    ```bash
    $ grep -rl "virus" /home
    ```

5.  **Context (`-C`):**
    Sometimes finding the line isn't enough. You want to see what happened before and after.
    *   `-C 2`: Shows 2 lines of context (before and after).
    *   `-A 2` (After): 2 lines after.
    *   `-B 2` (Before): 2 lines before.
    ```bash
    $ grep -C 3 "CRITICAL ERROR" server.log
    ```

### Introduction to Regular Expressions (Regex) with grep
`grep` supports advanced search patterns called Regex. They are a language in themselves, but here are the two most useful:

*   `^` (Caret): Means **"Start of line"**.
    `grep "^root" /etc/passwd` -> Finds lines *starting* with root.
*   `$` (Dollar): Means **"End of line"**.
    `grep "bash$" /etc/passwd` -> Finds lines *ending* in bash.

@quiz: You want to search for the word "configuration" inside all files in your current directory and subdirectories, ignoring case. Which command is correct?
@option: find . -name "configuration"
@option: grep -r "configuration" .
@correct: grep -ri "configuration" .
@option: locate configuration

@section: Part 5: Combining Powers (Pipes)

The true power of Linux emerges when connecting these commands.

**Example 1: Count results**
How many JPG files do I have?
```bash
find . -name "*.jpg" | wc -l
```
(`find` searches, `wc -l` counts result lines).

**Example 2: Search in results**
Find java processes and then search which ones are user "admin".
```bash
ps aux | grep "java" | grep "admin"
```

**Example 3: Find a command**
Sometimes you want to know where a program is installed.
*   `which python`: Tells you the path of the main executable (`/usr/bin/python`).
*   `whereis python`: Tells you executable, source code, and manual.

@section: Part 6: sed and awk (LPIC-1 depth)

`grep` finds lines; **`sed`** edits text streams; **`awk`** splits lines into fields and can filter, sum, and format—classic exam one-liners.

### sed

```bash
sed 's/old/new/' file.txt          # first match per line
sed 's/foo/bar/g' file.txt         # all matches per line
sed '/^#/d' /etc/nginx/nginx.conf  # delete matching lines
sed -n '10,20p' /var/log/syslog    # print line range
```

### awk

```bash
ps aux | awk '{print $1}'          # first column
awk '$3 > 100' measurements.txt    # rows where field 3 > 100
```

See `man sed`, `man awk` / `info gawk`.

@quiz: Which sed form replaces every occurrence of foo with bar on each line?
@option: sed 's/foo/bar/' file
@correct: sed 's/foo/bar/g' file
@option: awk '/foo/bar/g'

@quiz: In awk, what does $1 usually mean?
@option: Last field
@correct: First field (per the field separator)
@option: Line number

@section: Practical Lab: The Case of the Full Server

Let's simulate a real admin situation.

1.  **The problem:** Your home folder seems full.
2.  **Step 1:** Use `find` to locate culprits (files over 50MB).
    ```bash
    $ find ~ -size +50M
    ```
3.  **Step 2:** You see many giant `.log` files. You want to check if they are important. Search for word "Error" in them.
    ```bash
    $ grep "Error" *.log
    ```
4.  **Step 3:** You confirm they are trash. You decide to delete them.
    ```bash
    # First list to be sure
    $ find ~ -name "*.log" -size +50M
    # Then execute deletion
    $ find ~ -name "*.log" -size +50M -exec rm {} \;
    ```

Congratulations! You cleaned the system using advanced search.

@section: Summary / Cheat Sheet

| Tool | Ideal Use | Example Command |
| :--- | :--- | :--- |
| **Wildcards** | Select multiple files in current directory | `ls *.jpg` |
| **locate** | Instant file search by name (index) | `locate file` |
| **find** | Precise and real-time search (size, date, type) | `find . -name "*.txt"` |
| **grep** | Search text **INSIDE** files | `grep -r "text" .` |
| **which** | Find where a program is installed | `which firefox` |

Now you have X-Ray vision over your system. Nothing can hide from you anymore.