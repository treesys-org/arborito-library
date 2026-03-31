@title: The Hacker's Compass: pwd, cd, ls & Navigation
@icon: 🗺️
@description: The definitive guide to stop fearing the black screen. Learn to move through the file system at the speed of thought.
@order: 1

# The Hacker's Compass: Mastering Terminal Navigation

@section: LPIC-1 map — Module 2 (GNU & Unix commands, FHS)

Alignment with **LPIC-1 (103 / 104)**:

*   **103.1 Shell and command line:** `pwd`, `cd`, paths, history, quoting.
*   **103.2 Text streams:** redirections and pipes (completed in `Data_Streams`); filters in `Search_Filter`.
*   **103.3 Basic file management:** `ls`, `cp`, `mv`, `rm`, links (other lessons in this module).
*   **104.1 FHS:** what belongs in `/bin`, `/etc`, `/var`, `/home`.
*   **RHEL vs Debian:** same GNU tools; **config paths** differ—check `/etc/os-release`.

**Full FHS deep dive:** the complete “mansion map”—`/boot`, `/proc`, `/sys`, `/run`, `/usr/local`, the `/usr` merge, `/tmp` vs `/var/tmp`, LPIC table, and lab—is written as a manual-style chapter in **Disks and Files: The Ultimate Guide** (Module 1, `03_Installation.md`, section 2). This module uses those paths every day with `cd` and `ls`; that lesson explains **why** they exist.

Welcome to Module 2. If you are here, you have survived the installation and understand what Linux is. But now you face the famous "Black Screen" (the Terminal).

For a new user coming from Windows or macOS, the terminal can seem like a dark cave.
*   In the graphical environment (GUI), if you want to open a folder, you **see** it and double-click.
*   In the terminal (CLI), you see nothing until you ask to see it. You are "blind" by default.

**Don't panic.**

Navigating the terminal isn't hard; it's just different. In fact, once you learn, you'll realize that using a mouse to find a file in a hierarchy of 10 folders is incredibly slow and painful. The terminal is teleportation.

In this massive lesson, we are going to turn you into a mobility expert. We will break down each command piece by piece and explain not just *how* they work, but *why* they exist and the tricks professionals use to avoid typing so much.

@section: 1. The Mental Concept: The Inverted Tree

Before typing a single letter, you need to visualize where you are.

The Linux file system is an **Inverted Tree**.
*   The roots are at the very top.
*   The branches (folders) hang downwards.
*   The leaves (files) are at the end of the branches.

In Windows, you are used to having multiple trees:
*   Tree C: (The system)
*   Tree D: (Your data)
*   Tree E: (Your USB)

In Linux, **there is only one giant tree**. Everything starts at a single point called **The Root**, represented by a simple forward slash: **`/`**.

No matter how many hard drives you have, they are all branches grafted onto this single grand tree. Your goal in this lesson is to learn how to climb these branches with agility.

@section: 2. `pwd`: The "You Are Here"

Imagine you wake up in an unknown room inside a giant castle. It's dark. You don't know if you are in the dungeon, the tower, or the kitchen.
What is the first question you ask yourself?
*"Where am I?"*

In Linux, that question is asked with the command `pwd`.

### Meaning
`pwd` stands for **Print Working Directory**.
*   **Print:** In old computing, "print" meant showing text on the screen (not necessarily on paper).
*   **Working Directory:** It is the folder you are "standing in" right now.

### Let's try it
Open your terminal and type:

```bash
$ pwd
```

The answer will be something like:
`/home/your_user`

### Anatomy of a Path
That answer `/home/your_user` is a **Path**. Let's dissect it:
1.  The first `/`: Represents **The Root**. It is the beginning of the universe.
2.  `home`: It is a folder inside the root.
3.  The second `/`: It is just a separator. It means "inside of".
4.  `your_user`: It is a folder inside `home`.

So `/home/your_user` reads: *"Start at Root, enter the home folder, and then enter the your_user folder"*.

**Why is this important?**
Because any command you execute (create a file, delete a photo) will happen **HERE**, in your working directory. If you don't know where you are, you might accidentally delete things you didn't mean to.
**Golden Rule:** If you feel lost, type `pwd`.

@section: 3. `ls`: The System's Eyes

Now you know where you are, but the room is dark. You need to turn on the light to see what's around you.
That light switch is the `ls` command.

### Meaning
`ls` comes from **List**. Its job is to list the files and folders in your current location.

### Level 1: Basic `ls`
Simply type:

```bash
$ ls
```

You will see something like:
`Documents  Downloads  Pictures  Music  Videos  note.txt`

**The Color Code:**
Most modern terminals help you with colors (although this may vary by configuration):
*   **Blue:** Directories (Folders).
*   **White (or black):** Text or normal files.
*   **Green:** Executable files (Programs or scripts).
*   **Cyan:** Symbolic links (Shortcuts).
*   **Red:** Compressed files (.zip, .tar.gz).

### Level 2: `ls -a` (X-Ray Vision)
In Linux, there are files that are shy. They are called **Hidden Files**.
Any file or folder whose name starts with a dot (`.`) is hidden. The system uses them to save configurations without bothering you.

If you do a normal `ls`, you don't see them. To see them, you need the option (flag) `-a` (**All**).

```bash
$ ls -a
.  ..  .bashrc  .profile  .config  Documents  Downloads
```

Wow! A bunch of new things appeared.
*   `.bashrc`: This is your terminal configuration file.
*   `.config`: Where your applications save their preferences.
*   `.` and `..`: We will explain these two mysterious symbols later, but they are vital.

### Level 3: `ls -l` (The Forensic Report)
Sometimes, knowing the file name is not enough. You want to know:
*   How big is it?
*   When was it created?
*   Who is the owner?
*   Do I have permission to open it?

For that, we use the `-l` option (**Long Format**).

```bash
$ ls -l
total 64
drwxr-xr-x 2 john john 4096 Oct 25 10:00 Documents
-rw-r--r-- 1 john john  520 Oct 26 14:30 note.txt
```

This looks like Matrix code, but let's read it together. Let's take the `note.txt` line:

1.  `-rw-r--r--`: **Permissions**.
    *   The first character tells us what it is. If it's a dash `-`, it's a file. If it's a `d`, it's a directory.
    *   The rest (`rw-r--r--`) says who can read and write (we'll see this in Module 3).
2.  `1`: Number of links (ignore for now).
3.  `john` (first): The **User** owner of the file.
4.  `john` (second): The **Group** owner of the file.
5.  `520`: The **Size** in bytes.
6.  `Oct 26 14:30`: Date and time of last modification.
7.  `note.txt`: The name.

### Level 4: `ls -h` (Humanizing Data)
Do you know how much 10485760 bytes is? Me neither.
Computer scientists hate mental math. That's why there is the `-h` option (**Human Readable**).
This option converts those ugly numbers into `K` (Kilobytes), `M` (Megabytes), or `G` (Gigabytes).

But `-h` alone isn't very useful because normal `ls` doesn't show sizes. You have to combine it with `-l`.

**The Ultimate Combo:**
In Linux, you can join option letters. Instead of writing `ls -l -a -h`, you can write:

```bash
$ ls -lah
```

This is the command you will use 90% of the time. It shows you **everything** (including hidden), in **detailed** format, and with **readable** sizes.

### Level 5: Ordering Chaos
When you have 500 files, an alphabetical list is not always best.
*   `ls -lt`: Sort by **Time**. Newest first. Useful to see what you just downloaded.
*   `ls -lS`: Sort by **Size**. Largest first. Useful for cleaning disk.
*   `ls -lr`: Sort **Reverse**. Put it at the end of any other command (e.g., `ls -ltr`) to flip the list.

@quiz: You just downloaded a file but don't remember the name. Which command would show you the files in the current folder sorted by date, putting the most recent at the top?
@option: ls -lS
@correct: ls -lt
@option: ls -la
@option: pwd

@section: 4. `cd`: The Teleporter

You know where you are (`pwd`) and what's around you (`ls`). Now you want to go somewhere else.
We use `cd`, which stands for **Change Directory**.

The `cd` command is your spaceship. But to fly, you need to give it coordinates. These coordinates are called **Paths**.

There are two ways to give coordinates: **Absolute** and **Relative**. Understanding the difference is what separates a novice from an intermediate user.

### A. Absolute Paths (The GPS)
An absolute path is the complete and exact address from the origin of the universe (the Root `/`).
It's like giving someone a full postal address: *"123 Fake Street, City, Country, Planet Earth"*.
No matter where you are in the world; that address always points to the same mailbox.

Absolute paths **ALWAYS start with `/`**.

Examples:
*   `cd /home/john/Downloads`
*   `cd /var/log`
*   `cd /etc`

**Advantage:** They never fail. You always know where you are going.
**Disadvantage:** They are long to type.

### B. Relative Paths (Local Directions)
A relative path depends on where you are right now.
It's like saying: *"Go straight, turn at the second left, and enter the blue door"*.
If you are in Madrid, those instructions take you to a bakery. If you are in Tokyo, they take you to a river.

Relative paths **NEVER start with `/`**. They start with the name of the folder you want to enter.

**Practical Example:**
Imagine you are in `/home/john` and want to enter `Documents`.

*   Absolute Method: `cd /home/john/Documents` (Works, but you type a lot).
*   Relative Method: `cd Documents` (The system assumes you are looking inside your current folder).

### The Magic Symbols: `.` and `..`
Remember when we did `ls -a` and saw two strange folders called `.` and `..`?
They are not real folders. They are file system shortcuts.

1.  **The Dot `.` (Right Here):**
    The single dot represents the current directory.
    It seems useless now (`cd .` does nothing, you stay where you are), but it will be vital when we execute scripts (e.g., `./script.sh` means "run the script that is *right here*").

2.  **The Double Dot `..` (The Parent):**
    This is the most useful thing you will learn today. The two dots represent **the folder above** (the "parent" directory containing the current one).
    It is the way to "go back" or "exit the folder".

    **`..` Exercises:**
    *   You are in `/home/john/Documents`.
    *   You type: `cd ..`
    *   Now you are in `/home/john`.

    You can chain them:
    *   You are in `/home/john/Documents`.
    *   You type: `cd ../..` (Go up one, then go up another).
    *   Now you are in `/home`.

### C. `cd` Shortcuts that Save Your Life
Linux administrators are lazy. We hate typing. Here are the tricks to move at light speed:

1.  **Go Home (`cd`):**
    If you type `cd` and press Enter (with nothing else), the system instantly teleports you to your personal folder (`/home/your_user`). It's the "Home" button in video games. Use it if you get lost in the depths of the system.

2.  **The Tilde (`~`):**
    The symbol `~` is a synonym for "My Personal Folder".
    *   `cd ~/Photos` is the same as `cd /home/john/Photos`.
    *   It's great because it works from anywhere.

3.  **The "Last Channel" Button (`cd -`):**
    You know that button on the TV remote that returns you to the channel you were watching before?
    `cd -` does exactly that. It takes you to the last directory you were in.
    *   You are in `/etc/nginx`.
    *   You do `cd /var/www/html`.
    *   You forgot something in the previous folder. You do `cd -`.
    *   Boom! You are back in `/etc/nginx`.

@quiz: You are in the `/var/log` folder and want to go to the `/var` folder. What is the fastest way (fewest characters) to do it using a relative path?
@option: cd /var
@correct: cd ..
@option: cd .
@option: cd ~

@section: 5. The Most Important Key: TAB (Autocomplete)

If you take only one thing from this lesson, let it be this:
**NEVER TYPE A FULL FILE NAME.**

The terminal is smart. If you want to enter the folder `Important_Documents_2025_Final`, you don't have to type it all.
Just type: `cd Imp` and press the **TAB** key (Tabulator, left of Q).

*   **If it's the only match:** The system will fill in the rest of the name instantly and put a `/` at the end.
*   **If there are multiple matches** (e.g., `Documents` and `Downloads`): The system will beep or do nothing. Press TAB **twice** quickly. The system will show you a list of possible options. Type one more letter to break the tie (e.g., `cd Do`) and press TAB again.

Get used to pressing TAB constantly. It not only saves time but **prevents typos**. If you press TAB and it doesn't complete, you know immediately that you mistyped the beginning or the file doesn't exist.

@section: 6. Case Sensitivity

Here is where Windows users suffer.
In Windows, a file named `Photo.jpg` and `photo.jpg` are the same file. You cannot have both in the same folder. Windows ignores capitalization.

**In Linux, capitalization matters.**
*   `File.txt`
*   `file.txt`
*   `FILE.TXT`

These are **three completely different files**. They can coexist in the same folder without issues.
When typing commands, be very careful. `cd documents` will fail if the folder is named `Documents` (with uppercase D).
By default, user folders in Linux (`Documents`, `Downloads`, `Desktop`) start with uppercase.

@section: 7. The Enemy: White Spaces

In Linux, the space (spacebar) is a sacred character. It means **"here ends one command and begins the next argument"**.

Imagine you have a folder called `My Photos`.
If you type: `cd My Photos`
The system will think:
1.  Command: `cd`
2.  Argument 1: `My` (try to go to folder "My")
3.  Argument 2: `Photos` (won't know what to do with this)

It will give you an error: `bash: cd: My: No such file or directory`.

### How to deal with spaces
You have two options to tell the terminal that the space is part of the name:

1.  **Quotes:** Wrap it all.
    `cd "My Photos"`
    This is the easiest way for humans to understand.

2.  **Escape (Backslash `\`):**
    Put a `\` bar just before the space. This tells the terminal: "The next character is not special, it's just text".
    `cd My\ Photos`

**Trick:** Use **TAB**! If you type `cd My` and press TAB, the terminal is smart enough to autocomplete using backslashes automatically (`My\ Photos/`).

@section: 8. Practical Workshop: Your First Excursion

Let's put this all into practice. Open your terminal and follow these steps. Try to predict what will happen before doing it.

1.  **Orientation:**
    *   Command: `pwd`
    *   *You should see `/home/your_user`.*

2.  **Exploration:**
    *   Command: `ls -lah`
    *   *Check your hidden files and sizes.*

3.  **Maze Creation:**
    *   Command: `mkdir -p test/level1/level2`
    *   *(This creates a folder inside a folder inside another).*

4.  **Entering the Rabbit Hole:**
    *   Command: `cd test/level1/level2`
    *   Command: `pwd`
    *   *You will see the long path.*

5.  **Going Back Step by Step:**
    *   Command: `cd ..`
    *   Command: `pwd`
    *   *Now you are in `level1`.*

6.  **Going Home Fast:**
    *   Command: `cd`
    *   Command: `pwd`
    *   *You are back in `/home/your_user`.*

7.  **The "Last Channel" Trick:**
    *   Command: `cd -`
    *   *BAM! Back to `test/level1`.*

8.  **Cleanup:**
    *   Command: `cd` (go home so we don't delete the rug under our feet).
    *   Command: `rm -r test`
    *   *(We delete the experiment).*

@section: 9. Common Troubleshooting

**Error: "No such file or directory"**
*   *Cause:* You mistyped the name or path.
*   *Solution:* Did you use capitals correctly? Did you use TAB to autocomplete? Are you in the correct folder (do `ls`)?

**Error: "Permission denied"**
*   *Cause:* You are trying to enter a folder that is not yours (e.g., `/root` or another user's folder).
*   *Solution:* If you really need to enter and have admin rights, use `sudo ls /root`. (But careful, you can't do `sudo cd`, because `cd` is not a program, it's a shell function. To be root, use `sudo -i`).

**Error: "Not a directory"**
*   *Cause:* You tried to `cd` into a text file (e.g., `cd note.txt`).
*   *Solution:* `cd` is only for folders (directories). If you want to view the file, use `cat`, `less`, or `nano`.

@section: Summary / Cheat Sheet

Keep this table in mind:

| Command | Action | Analogy |
| :--- | :--- | :--- |
| `pwd` | Where am I? | GPS / "You Are Here" Map |
| `ls` | What is here? | Turn on the light |
| `ls -a` | Show hidden | X-Ray Glasses |
| `ls -lah`| Show all detailed | Forensic Report |
| `cd folder` | Enter folder | Open a door |
| `cd ..` | Go up one level | Exit the room |
| `cd` | Go home | "Home" Button |
| `cd -` | Go to previous | "Last Channel" Button |
| **TAB** | Autocomplete | Autocorrect (but good) |

Congratulations! You are no longer a lost tourist. You are an explorer with a map and compass. The terminal is yours.