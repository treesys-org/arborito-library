@title: The Art of Asking for Help: The Master Guide (man, info)
@icon: 🆘
@description: The definitive and exhaustive guide (+500 lines) to achieving self-sufficiency in Linux. Learn to decipher manuals, find lost commands, and understand the RTFM philosophy.
@order: 5

# The Art of Self-Sufficiency: You Will Never Be Alone Again

Welcome to the most important lesson of your career as a Linux user.

Yes, I know I've said that in other lessons. But this time it's different. In previous lessons I taught you how to *fish* for specific fish (moving files, creating folders). In this lesson, I'm going to teach you **how to make your own fishing rod**.

### The Myth of the "Linux Guru"
There is an urban legend that Linux experts ("Gurus" or "Greybeards") are geniuses with photographic memory who remember the 25,000 system commands and their 100,000 options.

**That is a lie.**

Nobody, absolutely nobody, memorizes all this. The human brain's storage capacity is limited and is better used to remember your partner's birthday or your favorite series plot.

The difference between a **Novice** and an **Expert** is not what they know by heart.
The difference is that when the Expert doesn't know something (which is 90% of the time), **they know exactly where to look for the answer in less than 5 seconds**, without leaving the terminal and without opening Google.

In Windows, when you get lost, you search Google or call tech support.
In Linux, the system comes with the complete instruction manual pre-installed. It's like your car came with a mechanic in the trunk.

In this massive guide, we are going to learn how to talk to that mechanic.

@section: 1. The RTFM Philosophy

Before typing, let's talk culture. If you enter a Linux forum or IRC chat and ask a basic question (like *"How do I copy a file?"*), someone will likely answer you with four letters, sometimes rudely:

**RTFM**

It means: **"Read The F***ing Manual"***.

Although the form is aggressive, the background holds a profound truth: **Self-sufficiency is a virtue.**
Linux respects those who try to help themselves first. Before asking another human (whose time is valuable), you must ask the machine (whose time is infinite).

The Linux help system is hierarchical. You have to follow this escalation order:
1.  **Quick Help (`--help`):** To refresh memory about an option.
2.  **The Manual (`man`):** To understand how a command works thoroughly.
3.  **Search (`apropos`):** When you don't know which command to use.
4.  **Info (`info`):** To study complex programs like a book.
5.  **Local Documentation (`/usr/share/doc`):** For advanced configurations.
6.  **Internet:** The last resort.

Let's master each level.

@section: Level 1: The Quick Lifesaver (`--help`)

You are writing a command. You know `ls` lists files. But you want it to sort by size and don't remember if the letter was `-S`, `-s` or `-size`. You don't want to read a whole book, you just want quick data.

Here enters the universal flag: `--help` (or sometimes `-h`).

Almost 99% of Linux commands accept this option.

```bash
$ ls --help
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
      --author               with -l, print the author of each file
  -b, --escape               print C-style escapes for nongraphic characters
...
```

### How to read help syntax
The first line is critical. It is called "Usage" and has its own grammar you must learn:

`Usage: command [OPTIONAL] <MANDATORY>`

1.  **Brackets `[]`**: Mean what is inside is **Optional**. You can use it or not.
2.  **Angle Brackets `<>` or nothing**: Mean it is **Mandatory**.
3.  **Ellipsis `...`**: Mean you can put **several** (a list).
4.  **Vertical Bar `|`**: Means **OR** (one or the other, but not both).

**Analysis Example:**
`Usage: cp [OPTION]... [-T] SOURCE DEST`

*   `cp`: The command.
*   `[OPTION]...`: You can put options (like `-r` or `-v`) or put none. Dots mean you can put many (`-rv`).
*   `SOURCE`: It is the source file. No brackets, so you **must** put it obligatorily.
*   `DEST`: It is the destination. Also mandatory.

**Short vs. Long Options:**
Look at this line from the `ls` example:
`-a, --all`

*   **`-a` (Short):** Uses a single dash and single letter. Fast to type. Can be combined (`-lah`).
*   **`--all` (Long):** Uses two dashes and a full word. More readable for scripts. Cannot be combined (`--allhuman` doesn't work).

**Mental Exercise:**
If you see `Usage: grep [OPTIONS] PATTERN [FILE...]`, what does it mean?
*   Can I use grep without options? Yes (brackets).
*   Can I use grep without pattern? No (no brackets).
*   Can I use grep without file? Yes (brackets). In that case, it will read from keyboard.
*   Can I put 50 files? Yes (ellipsis).

@section: Level 2: The Sacred Book (`man`)

`--help` is fine for a quick reminder. But if you want to **understand** the command, you need the Manual.
The "Man Pages" are the encyclopedia of Linux.

```bash
$ man ls
```

This will open a reader (pager) with the complete documentation.

### Navigation inside `man`
Since `man` uses the `less` program underneath, controls are the same you learned in file viewing lesson:

*   **Arrows / Enter:** Go down line by line.
*   **Space:** Go down a full page.
*   **b:** Go up a full page (Back).
*   **/**: Search text (e.g., type `/sort` and press Enter to search how to sort).
*   **n:** Go to next search match.
*   **q:** Exit (Quit). Important!

### Anatomy of a Man Page
All pages follow a rigid and standardized structure:

1.  **NAME:** Name and a one-line description.
2.  **SYNOPSIS:** Technical syntax (as we saw in `--help`).
3.  **DESCRIPTION:** Long explanation of what the program does.
4.  **OPTIONS:** Detailed list of every flag (`-a`, `-l`, etc.) and what exactly it does.
5.  **EXAMPLES:** (Sometimes). Real usage examples. Very valuable!
6.  **FILES:** What configuration files this program uses.
7.  **SEE ALSO:** Other related commands that might interest you.

### Manual Sections (The Secret of Numbers)
Sometimes, doing `man something`, you will see a number in parentheses, like `passwd(1)` or `passwd(5)`.
Or you will see there are two things named the same: command `passwd` (to change password) and file `/etc/passwd` (which stores users).

If you write `man passwd`, which one does it show?
By default, the command. But Linux organizes manual in **Numbered Sections** to avoid collisions.

**Vital sections:**
1.  **User Commands:** Normal programs (`ls`, `cp`, `passwd` command).
2.  **System Calls:** Kernel functions for programmers (advanced).
3.  **Library Functions:** For C programmers (`printf`).
4.  **Devices:** Special files in `/dev`.
5.  **File Formats:** Super useful! Explains syntax of config files (`passwd` file, `fstab`).
6.  **Games:** Yes, games have manuals.
7.  **Miscellaneous.**
8.  **Administration Commands:** Root tools (`fdisk`, `ifconfig`, `useradd`).

**How to travel between sections:**
If you want to see help for password *file* and not command, you must specify section 5:

```bash
$ man 5 passwd
```

If you don't know in which sections something is, use flag `-f` (or command `whatis`):
```bash
$ whatis passwd
passwd (1)           - change user password
passwd (1ssl)        - compute password hashes
passwd (5)           - the password file
```
There you see it exists in 1 and 5.

@quiz: You are editing `/etc/fstab` file and don't remember column format. Which command gives specific documentation on that file's format?
@option: man fstab
@option: man 1 fstab
@correct: man 5 fstab
@option: help fstab

@section: Level 3: The Searcher (`apropos`)

The problem with `man` is you need to know the command name.
But what if you want to "create a partition" but don't know the command is called `fdisk`?

Here enters `apropos`.
`apropos` searches keywords in descriptions of all manuals.

```bash
# What is that for partitioning called?
$ apropos partition
```

The system will return a list:
```
addpart (8)          - tell the kernel about the existence of a partition
fdisk (8)            - manipulate disk partition table
partx (8)            - tell the kernel about the presence and numbering of on-disk partitions
...
```
Aha! `fdisk` looks like what I seek ("manipulate disk partition table"). Now I can do `man fdisk`.

**Tip:** If `apropos` returns nothing or complains, it's possible manual database is not indexed. You can update it (as root) with command `mandb` or `updatedb`.

@section: Level 4: The Trap of "Built-ins"

Here is a trap all newbies fall into.
You try to search help about command `cd` (Change Directory).

You write:
```bash
$ man cd
No manual entry for cd
```
*(Note: In some distros there is an entry, but in many not).*

**How is this possible?** `cd` is the most basic command. No manual?

The reason is technical but fascinating.
There are two types of commands:
1.  **Executables (Binaries):** Real files on your hard drive (e.g., `/usr/bin/ls`). `man` documents these files.
2.  **Built-ins:** Functions living *inside* the Shell itself (Bash). Not a separate file. Since `cd` needs to change terminal state, it must be part of it.

To know if a command is a file or built-in, use command `type`:
```bash
$ type ls
ls is aliased to `ls --color=auto'

$ type /bin/ls
/bin/ls is /bin/ls

$ type cd
cd is a shell builtin
```

**How to ask help for Built-ins:**
If `man` doesn't work, use command `help` (which is another Bash built-in).

```bash
$ help cd
cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.
    ...
```

**Trap summary:**
*   If it is a program (`ls`, `grep`): Use `man`.
*   If it is part of shell (`cd`, `alias`, `history`, `if`): Use `help`.

@section: Level 5: The Library of Alexandria (`info`)

If `man` falls short, there is a higher level.
GNU project (creators of many Linux tools) decided `man` pages were too limited for complex programs. They invented **Info** system.

`info` documents are not flat pages. They are **Books with Hyperlinks**. They have chapters, subchapters, and indexes.

```bash
$ info coreutils
```

**Navigation in Info (A bit weird):**
Navigation in `info` predates web, so no mouse.
*   **Tab:** Move to next hyperlink (marked with `*`).
*   **Enter:** Enter selected link.
*   **n:** Go to next node (page/chapter) at same level.
*   **p:** Go to previous node.
*   **u:** Go Up a level towards main index.
*   **q:** Exit.

Nowadays `info` is used less because people prefer searching HTML docs online, but if on server without internet, `info` is most complete deep documentation existing on your hard drive.

@section: Level 6: The Hidden Documentation (`/usr/share/doc`)

There is a place on your hard drive that is like dusty attic where program authors leave notes.
It is directory `/usr/share/doc`.

For every program you install, a folder is created here.
```bash
$ cd /usr/share/doc
$ ls
```
Enter folder of some complex program, for example `apt` or `cron`.
Inside you will find treasures not in manual:
1.  **README:** Read me first. General info.
2.  **CHANGELOG:** History of changes. What's new in this version?
3.  **examples:** (Sometimes). A folder full of example configuration files.
    *   *Use case:* You are configuring DHCP server and don't know syntax. Copy file from `/usr/share/doc/isc-dhcp-server/examples/dhcpd.conf.example` to `/etc/dhcp/dhcpd.conf` and edit. Saves hours of typing!

@section: Level 7: Community Tools (tldr, cheat)

Linux community knows `man` pages are dry and hard to read.
That's why modern projects were born to give instant "Cheat Sheets".

These tools usually don't come preinstalled, but you should install them.

### `tldr` (Too Long; Didn't Read)
It is a client showing **only practical examples**.
While `man tar` explains magnetic tape history, `tldr tar` tells you:
*   "To compress: `tar -czvf file.tar.gz folder`"
*   "To decompress: `tar -xzvf file.tar.gz`"

### `cheat`
Similar to tldr, but allows creating your own local cheat sheets.

@section: Level 8: The Art of Googling

Sometimes manual has no answer because your problem is specific error ("Error 500 starting Apache").
Here you go to Internet. But must know how to search.

**Hierarchy of Reliable Sources:**

1.  **Arch Wiki:** Internet Bible. Even if not using Arch Linux, its documentation is best in world. If you want to know how WiFi works in Linux, search "Arch Wiki NetworkManager". Technical, precise, updated.
2.  **Gentoo Wiki:** Similar to Arch, very technical and detailed.
3.  **StackOverflow / Unix & Linux StackExchange:** For specific programming or error questions. Look at answers with green "tick", but read comments.
4.  **DigitalOcean Tutorials:** Surprisingly, this hosting company has best step-by-step tutorials for configuring servers (Nginx, Docker, etc.) for beginners.
5.  **Your Distro Forums (Ubuntu Forums, etc.):** Useful, but sometimes info is old. Always check **date** of post. A 2012 solution for Wifi will surely break your 2025 system.

**How to search errors:**
*   Copy EXACT error message. Put in quotes in Google.
    `"Kernel panic - not syncing: VFS: Unable to mount root fs"`
*   Add your distribution if relevant.
    `site:askubuntu.com "wifi not working"`

@section: Practical Workshop: Solve the Mystery

Let's play a game. You have to use tools learned. No looking at solution below.

**Mission 1:** I don't remember command name to change user password.
*   *Tool:* `apropos`
*   *Action:* `apropos password`
*   *Result:* Search list and find `passwd`.

**Mission 2:** I want to use `ls` but want directories to appear first, before files. Don't know option.
*   *Tool:* `man` or `--help`
*   *Action:* `man ls` and search (`/`) word "directory". Or "group".
*   *Result:* Find `--group-directories-first`.

**Mission 3:** What is system configuration file for DNS servers? I know it has to do with "resolver".
*   *Tool:* `apropos` then `man` section 5.
*   *Action:* `apropos resolver`. You see `resolv.conf (5)`.
*   *Action:* `man 5 resolv.conf`.
*   *Result:* Tells you file is `/etc/resolv.conf` and explains format.

@section: Summary / Cheat Sheet

| Tool | When to use | Example Command |
| :--- | :--- | :--- |
| **--help** | Quick option reminder (flags) | `ls --help` |
| **man** | Understand command deeply | `man grep` |
| **man [N]** | See file formats (config) | `man 5 passwd` |
| **apropos** | Don't know command name | `apropos "partition"` |
| **type** | Know if program or built-in | `type cd` |
| **help** | Help for shell built-ins | `help cd` |
| **info** | Complex navigable docs | `info coreutils` |
| **tldr** | Just examples please | `tldr tar` |

Congratulations. You now have most important superpower of all: **Capacity to learn anything without anyone's help.**
You are no longer a lost user. You are a researcher.