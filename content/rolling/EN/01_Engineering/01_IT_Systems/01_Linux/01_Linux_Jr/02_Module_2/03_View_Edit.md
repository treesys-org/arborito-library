@title: View and Edit: The Master Text Guide
@icon: 📝
@description: The definitive guide (+500 lines) to mastering text file manipulation in the terminal. From 'cat' to 'vim' mastery.
@order: 3

# The Art of Text: Reading and Writing in the Terminal

Welcome to one of the most critical skills you will acquire as a Linux user.

In Windows or macOS, you are used to files opening with heavy, specific programs: Word for `.docx`, Notepad for `.txt`, Excel for `.xlsx`. It's a "File-Program" relationship.

In Linux, the philosophy is different. **Everything is text**.
Your network configuration is text. The user list is text. System logs are text. Scripts automating servers are text. Even information about your hardware is presented as virtual text.

If you master the tools to read and edit text in the terminal, you master the operating system. You don't need graphical interfaces. You don't need a mouse. You only need your eyes and your keyboard.

This guide is extensive because the topic requires it. We will cover from how to read a quick note to how to survive in `vim`, the editor that has trapped millions of newbies (literally, they didn't know how to exit).

@section: 1. Rapid Viewing: The 'cat' Family

Sometimes you just want to see what's inside a file without opening an editor. You want to dump the content onto your screen. For that, we have a family of very useful commands.

### 1.1 `cat` (Concatenate)
It's the most famous command, but often misunderstood. Its name comes from "Concatenate" (join), although 99% of people use it to "show".

**Basic Usage:**
```bash
$ cat file.txt
```
This prints the entire file content to standard output (your screen) at once.

**The Problem with `cat`:**
If the file has 50,000 lines, `cat` will print them all at lightning speed. Your terminal will fill with text scrolling so fast you won't be able to read anything, and in the end, you'll only see the last 20 lines.
*   **Rule:** Use `cat` only for small files (short config files, brief notes).

**Advanced `cat` Uses:**
*   **Number lines:** Sometimes you want to know what line number something is on.
    ```bash
    $ cat -n code.py
    ```
*   **Create quick files:** You can use `cat` to write short files without opening an editor, using redirection.
    ```bash
    $ cat > shopping_list.txt
    Milk
    Bread
    Eggs
    (Press Ctrl+D to save and exit)
    ```
    *Explanation:* The `>` symbol redirects what you type on the keyboard into the file. `Ctrl+D` sends the "End Of File" (EOF) signal.

### 1.2 `tac` (Cat reversed)
Yes, Unix programmers have a peculiar sense of humor.
`tac` does exactly the same as `cat`, but prints lines **in reverse order**. The last line appears first.

**What is this for?**
Imagine a chronological log file where the newest entry is written at the end. If you want to see the latest first, `tac` is your friend.

```bash
$ tac events.log
```

### 1.3 `nl` (Number Lines)
It is a more specific alternative to `cat -n`. Its only job is to number lines.
```bash
$ nl script.txt
```

@section: 2. Controlling the Flow: Pagers (less and more)

When files are too big for `cat`, **Pagers** come into play. They are programs that allow you to read text interactively, scrolling up and down, without loading the entire file into memory at once.

### 2.1 `more` (The Grandfather)
It was the first pager. Nowadays it's obsolete, but still present in almost all systems for compatibility.
*   Only allows going forward (pressing Space).
*   Does not allow going back easily.
*   When it reaches the end, it closes itself.
*   *Advice:* Don't use it unless you don't have `less`.

### 2.2 `less` (The Modern Standard)
There is a saying in Linux: **"less is more"**.
`less` is an improved version of `more`. It is incredibly powerful.

**Basic Usage:**
```bash
$ less giant_file.log
```

When you run it, the terminal becomes an ebook reader.

**Navigation Guide in `less` (Memorize this!):**

| Action | Key |
| :--- | :--- |
| **Down a line** | `Enter` or `Down Arrow` or `j` |
| **Up a line** | `Up Arrow` or `k` |
| **Down a page** | `Space` or `PgDn` |
| **Up a page** | `b` or `PgUp` |
| **Go to end** | `G` (Uppercase) |
| **Go to start** | `g` (Lowercase) |
| **Search text** | `/text_to_search` (then press `n` for next, `N` for previous) |
| **Help** | `h` |
| **EXIT** | `q` (Quit) |

**Why `less` is great:**
`less` doesn't load the whole file into RAM. If you have a 100 GB log file (yes, happens on servers), `cat` would hang your computer trying to read it. `less` opens it instantly because it only reads the part you are seeing on screen.

@section: 3. Looking at the Ends: head and tail

Often you don't want to read the whole book. You just want to read the prologue or the epilogue.

### 3.1 `head` (The Head)
Shows the first lines of a file. By default, the first 10.

```bash
$ head /etc/passwd
```

**Customizing the amount:**
Use the `-n` option to specify how many lines you want.
```bash
$ head -n 5 /etc/passwd  # Shows the first 5
```

### 3.2 `tail` (The Tail)
Shows the last lines of a file. By default, the last 10.
This is vital in system administration because recent errors in logs are always at the end.

```bash
$ tail /var/log/syslog
```

### 3.3 `tail -f` (Follow Mode)
This is perhaps the function most used by administrators worldwide.
The `-f` option stands for **Follow**.

When you run `tail -f file.log`:
1.  Shows the last 10 lines.
2.  **Does NOT close.** It stays waiting.
3.  If any program writes a new line to that file, `tail` prints it to your screen instantly.

It's like watching a live broadcast of what's happening to your server.

**Real usage example:**
You are trying to start a web server and it fails.
1.  Open a terminal.
2.  Type: `tail -f /var/log/nginx/error.log`
3.  In another terminal, restart the server.
4.  You see the error appear in real-time in the first terminal.
5.  Press `Ctrl + C` to exit follow mode.

@quiz: You are monitoring a server and want to see new errors as they happen in the 'error.log' file. Which command do you use?
@option: cat error.log
@option: less error.log
@correct: tail -f error.log
@option: head -f error.log

@section: 4. Text Editors: CLI Philosophy

We know how to read. Now it's time to write.

Editing files in the terminal is intimidating at first because **you don't have a mouse**. You can't click in the middle of a sentence to fix a typo. You have to use arrows or keyboard shortcuts to move the cursor.

There are two main "religions" in the world of terminal editors:
1.  **nano:** Easy, intuitive, for normal humans.
2.  **vi / vim:** Powerful, complex, for keyboard wizards.

We will learn both. You need `nano` to survive today, and you need to understand `vi` to survive tomorrow.

@section: 5. `nano`: The Friendly Editor

If you are new to Linux, **use nano**. Don't try to be a hero yet.
`nano` was designed to be a replacement for the old `pico` editor. Its philosophy is: "The screen should tell you what to do".

### Starting nano
```bash
$ nano my_file.txt
```
If the file doesn't exist, `nano` will create it in memory (it is not saved to disk until you say so).

### The nano Interface
At the top you see the version and file name.
In the center, the text area.
**At the bottom**, you see a "Help Bar". This is what makes nano great.

You will see things like:
`^G Get Help`  `^O Write Out`  `^X Exit`

The `^` (caret) symbol represents the **Control (Ctrl)** key.
So `^X` means you must press `Ctrl + X`.

### Essential nano Commands

1.  **Write:** Simply type. Use arrows to move.
2.  **Save:** It's called "Write Out".
    *   Press `Ctrl + O`.
    *   Nano will ask: `File Name to Write: my_file.txt`.
    *   Press `Enter` to confirm.
3.  **Exit:**
    *   Press `Ctrl + X`.
    *   If you have unsaved changes, it will ask: `Save modified buffer? (Y/N)`.
        *   Press `Y` for yes, `N` for no.
        *   If you pressed `Y`, it will ask to confirm the file name. Press `Enter`.
4.  **Search Text:**
    *   Press `Ctrl + W` (Where is).
    *   Type the word and press `Enter`.
5.  **Cut and Paste (Nano Style):**
    *   Nano doesn't use the normal system clipboard by default.
    *   `Ctrl + K` (Cut Text): Cuts (deletes) the entire line where the cursor is.
    *   `Ctrl + U` (Uncut Text): Pastes the line you just cut.

### Configuring nano
Nano can have syntax highlighting (colors) for programming. Usually it activates automatically if the file has an extension (e.g. `.py` or `.html`).
If you want to configure it, its file is `~/.nanorc`.

**Why use nano?**
*   It is installed almost everywhere.
*   Requires no memorization; instructions are on screen.
*   It is fast and light.

@section: 6. `vi` and `vim`: The Beast

Now we enter legendary territory. `vi` (Visual Editor) was born in 1976. `vim` (Vi IMproved) was born in 1991.
It is the standard Unix editor. It is on **all** Linux systems, from your WiFi router to Google's most powerful server. Sometimes, on minimal rescue systems, `nano` isn't there, but `vi` always is.

**You have to know basic vi to not get stuck.**

### The Modal Philosophy
Here is where people get confused. Normal editors (Notepad, nano, Word) have a single mode: if you type an 'a', an 'a' appears on the screen.

**Vim has MODES.**
Depending on the mode you are in, the 'a' key might mean "type letter a" or it might mean "append text after cursor".

The 3 main modes:
1.  **Normal Mode:** The default mode upon opening. **Here you cannot type text.** Keys are commands to move, delete, copy, or paste. It is the "control mode".
2.  **Insert Mode:** Here is where you write normal text. Behaves like a classic editor.
3.  **Command Line Mode:** Serves to give orders to the editor (save, exit, search). Entered by pressing `:`.

### Vim Survival Guide (Step by Step)

Let's simulate a session. Don't just read this, **do it** in your terminal.

**Step 1: Enter**
```bash
$ vi test_vim.txt
```
Now you are in **Normal Mode**. If you try to type "Hello", weird things will happen and the computer will beep. Don't panic.

**Step 2: Write (Enter Insert Mode)**
To start writing, you need to change mode.
*   Press the `i` key (for Insert).
*   Look at the bottom left corner. It should say `-- INSERT --`.
*   Now you can type: "Hello world, this is vim."

**Step 3: Back to Control (Exit Insert Mode)**
You finished writing. You want to save. But you can't save while writing. You have to go back to Normal Mode.
*   Press the `Esc` key.
*   The `-- INSERT --` text disappears. You are back in Normal Mode.

**Step 4: Save and Exit (Command Mode)**
From Normal Mode:
*   Press `:` (colon). You'll see a `:` appear at the very bottom. The cursor waits for you there.
*   Type `w` (Write / Save).
*   Press `Enter`. Vim will say something like "test_vim.txt written".
*   Press `:` again.
*   Type `q` (Quit).
*   Press `Enter`.

Congratulations! You survived your first edit.

**The Pro Shortcut:**
You can combine commands. To save and exit at once:
*   `Esc`
*   `:wq`
*   `Enter`

**Step 5: Exit WITHOUT saving (Panic)**
Imagine you deleted half the file by mistake and want to exit without saving changes.
*   `Esc` (To ensure you are in normal).
*   `:q`
*   Vim will yell at you: *"No write since last change (add ! to override)"*. It protects you.
*   To force exit: `:q!` (Quit Bang!).

### Movement in Normal Mode
Why do people love Vim? Because in Normal Mode you can move and edit at the speed of thought without touching the mouse.

*   **h, j, k, l:** Move cursor (Left, Down, Up, Right). Why not arrows? Because old keyboards didn't have arrows, and because this way you don't move hands from typing position. (Arrows work, but pros use hjkl).
*   **w:** Jump to next **w**ord.
*   **b:** Jump **b**ack a word.
*   **0:** Go to start of line.
*   **$:** Go to end of line.
*   **gg:** Go to start of file.
*   **G:** Go to end of file.

### Fast Editing in Normal Mode
*   **x:** Deletes character under cursor.
*   **dd:** Deletes (cuts) current line.
*   **u:** Undo. The lifesaver!
*   **Ctrl + r:** Redo.
*   **yy:** Copies (Yank) current line.
*   **p:** Pastes what you cut or copied after cursor.

**Vim Grammar:**
Vim is a language. Commands can be combined with numbers and movements.
*   `d` (delete) + `w` (word) = `dw` (Delete a word).
*   `d` (delete) + `$` (end of line) = `d$` (Delete to end of line).
*   `2` (twice) + `dd` (delete line) = `2dd` (Delete two lines).
*   `100` + `j` = Down 100 lines.

Once you internalize this "grammar", editing text becomes fluid and extremely fast.

### `vimtutor`
Vim comes with a fantastic interactive tutorial. If you want to really learn, type in your terminal:
```bash
$ vimtutor
```
It will take about 30 minutes to complete and will teach you more than any book.

@quiz: You are trapped in `vi` and don't know what mode you are in. You want to exit without saving anything. What exact sequence of keys should you press?
@option: Ctrl+C, then :exit
@correct: Esc, then :q!, then Enter
@option: Ctrl+X, then N
@option: Type "exit" and press Enter

@section: 7. Comparison: Which one to choose?

| Feature | nano | vi / vim |
| :--- | :--- | :--- |
| **Learning Curve** | Flat (5 minutes) | Vertical (Weeks) |
| **Interface** | Visible menus, Ctrl shortcuts | Invisible modes, one-key shortcuts |
| **Mouse Usage** | No (generally) | No |
| **Editing Speed** | Normal | Extremely high (for experts) |
| **Availability** | Very common | Universal (it's on everything) |
| **Ideal Use** | Quick edits, newbies | Programming, Professional SysAdmin |

**My advice:**
1.  Use `nano` to start. Don't get frustrated. Edit your config files with nano.
2.  Learn the minimum `vi` commands (`i`, `Esc`, `:wq`, `:q!`) in case you ever find yourself on a server that doesn't have nano.
3.  If you plan to do this professionally, dedicate a week to learning `vim`. Your hands will thank you in the long run.

@section: 8. Other Useful Text Commands

Aside from viewing and editing, there are tools to manipulate text in "pipes".

*   `wc` (Word Count): Counts lines, words, and characters.
    *   `wc -l file.txt`: Counts only lines.
*   `sort`: Sorts file lines alphabetically.
    *   `sort names.txt`
*   `uniq`: Removes **consecutive** duplicate lines. (Usually used after `sort`).
*   `diff`: Compares two files and tells you differences line by line.
    *   `diff version1.conf version2.conf`

@section: 9. Summary / Cheat Sheet

**Viewing:**
*   `cat`: Small files, all at once.
*   `less`: Large files, paging, search (`/`), exit with `q`.
*   `head`: Start of file.
*   `tail`: End of file.
*   `tail -f`: See logs in real-time.

**Editing with Nano:**
*   `Ctrl + O`: Save.
*   `Ctrl + X`: Exit.
*   `Ctrl + W`: Search.

**Editing with Vim:**
*   `i`: Insert Mode (Write).
*   `Esc`: Back to Normal Mode.
*   `:w`: Save.
*   `:q`: Exit.
*   `:q!`: Exit without saving (Force).
*   `:wq`: Save and Exit.
*   `dd`: Delete line.
*   `u`: Undo.

Now you have the power to control any text file on any Unix system in the world!