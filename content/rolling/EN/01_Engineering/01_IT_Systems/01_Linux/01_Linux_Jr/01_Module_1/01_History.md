@title: History: The Epic of Open Code
@icon: 📜
@description: The definitive and detailed history: from mainframe priests and corporate Unix wars to the global Linux revolution.
@order: 1

# The Epic of Code: From Punch Cards to Global Conquest

@section: LPIC-1 map — Module 1 (coverage reference)

This module supports typical **LPIC-1 (101)** objectives around **system architecture**, **kernel vs userspace**, **boot chain**, and **installation**. It does not replace the official LPI blueprint; it is a study compass.

*   **101 — System architecture:** why Linux exists; how distributions differ; kernel role.
*   **101 — Boot concepts:** historical context for SysV vs **systemd** (expanded in later lessons in this module).
*   **102 — Installation:** partitioning, boot loaders, targets (see `Installation` and `Boot` lessons).
*   **RHEL vs Debian:** package names and paths differ; **concepts** stay the same.

Welcome to the true history of modern computing.

It is easy to fall into the trap of thinking that Linux is just "another operating system," a free alternative to Windows used by servers. But that is a superficial and boring view. Linux is the improbable result of a series of ideological rebellions, lucky accidents, massive egos, and corporate wars spanning more than half a century.

To understand why your terminal works the way it does, why you type `ls` instead of `dir`, why the system is free, or why the Internet doesn't collapse, we need to dig deep into the past. This isn't a list of dates; it's the story of how a group of hippies, academics, and hackers defeated the biggest corporations on the planet using only text.

@section: 1. The Iron Age: Priests and Mainframes (1960s)

Let's travel back to the mid-1960s. The concept of a "Personal Computer" (PC) was pure science fiction, something only seen in *Star Trek*.

Computing was dominated by **"Big Iron"**: Mainframes. They were colossal machines, owned by titans like IBM, DEC (Digital Equipment Corporation), or General Electric. They lived in refrigerated sanctuaries called "Computing Centers," attended by a caste of technicians in white coats who acted as priests between humans and the machine.

### The Tyranny of "Batch Processing"
If you were a scientist or programmer in 1965, your life was miserable. You didn't have a keyboard on your desk. You didn't have a screen. Interaction with the machine was asynchronous, bureaucratic, and frustrating:

1.  **Analog Coding:** You wrote your program on sheets of grid paper with a pencil.
2.  **Punching:** You took those sheets to a typing room where operators transferred your code onto rigid cardboard **punch cards**. Each card represented a single line of code. A complex program was a shoebox full of cards.
3.  **The Window:** You handed your box to an operator. He put it in a physical queue next to those of other programmers.
4.  **The Process (Batch):** The machine read the cards one after another, executed the programs in series, and spat the results out on a line printer.
5.  **The Wait:** You could wait 4, 12, or 24 hours to get results.
6.  **The Horror:** If you had made **a single syntax error** (a misplaced comma), the program failed instantly. You received a sheet of paper with the error and had to repeat the entire process from step 1.

This slowness stifled creativity. Programmers spent 90% of their time waiting and 10% working. Computing was exclusive, expensive, and slow.

@section: 2. The Dream and Fall of Multics

In this context of frustration, an alliance of titans emerged: **MIT** (academic brains), **General Electric** (powerful hardware), and **AT&T Bell Labs** (research and telecommunications).

Their dream was to build the computing utopia: **Multics** (Multiplexed Information and Computing Service).
The idea was revolutionary: to turn computing into a "public utility," like electricity or water.
*   A gigantic and omnipotent central mainframe.
*   Hundreds of dumb terminals (keyboard and screen/printer) in users' offices, connected by telephone lines.
*   **Time-Sharing:** The CPU would switch so fast between users that everyone would feel like they had the machine to themselves in real-time.

### The Failure
Multics fell victim to what is called in engineering "The Second System Effect": trying to do everything perfectly from the start.
It was monstrously complex. They tried to write it in a new and unproven language called **PL/1**. The hardware of the time could barely move it. The system took minutes to boot, consumed too much memory, and crashed constantly.

In **1969**, Bell Labs executives looked at the bills, saw they were throwing millions of dollars into a bottomless pit, and made an executive decision: **Kill the project**. Bell Labs withdrew from Multics.

@section: 3. From the Ashes: The Birth of UNIX (1969)

The cancellation of Multics left a group of brilliant engineers at Bell Labs without a project and, worse, without their time-sharing "toy." Among them were **Ken Thompson**, **Dennis Ritchie**, and **Brian Kernighan**.

They were used to the real-time interaction of Multics and refused to return to the tyranny of punch cards. Furthermore, Ken Thompson had written a video game called *Space Travel* (a solar system simulation) and wanted to play it. Running it on the company mainframe cost $75 per game (about $600 in today's money). They needed their own machine.

They found an old DEC **PDP-7** minicomputer abandoned in a hallway. It had barely 8KB of memory (yes, kilobytes). It was a ridiculous machine compared to mainframes, but it was free, and nobody was watching it.

### The Summer of '69
While his wife and son went on vacation to California, Ken Thompson locked himself in the lab for a frenetic month (August 1969). He decided to write an operating system for that PDP-7, but learning from the mistakes of Multics.
Instead of making it complex and grandiose, he would make it simple, small, and modular. **KISS: Keep It Simple, Stupid**.

In four weeks he wrote:
1.  A basic Kernel to manage the CPU.
2.  A hierarchical file system (folders within folders).
3.  A command interpreter (Shell).
4.  An editor and an assembler.

**UNICS** (Uniplexed Information and Computing Service) was born, a mocking pun on "Multics" (Uniplexed = One, Multiplexed = Many). Later, it was spelled **UNIX**.

### The Epoch
A curious detail: To measure time, Unix counts the seconds that have passed since an arbitrary date. That date is **January 1, 1970**. That is the "Big Bang" for your computer. If you type `date +%s` in your terminal today, you will see the seconds elapsed since that moment.

@section: 4. The C Language Revolution (1972)

At first, Unix had a fatal flaw: it was written in **Assembly Language**.
Assembly is direct instructions to the CPU (move this bit here, add these registers). It is extremely fast, but **it is not portable**. The Assembly of a PDP-7 machine is totally different from that of an IBM or UNIVAC machine.
*   *The Problem:* If they wanted to bring Unix to a new and more powerful computer (like the new PDP-11), they had to **rewrite the entire operating system from scratch**.

Dennis Ritchie, frustrated by this, decided to create a new "high-level" programming language that allowed hardware control but was readable by humans and portable between machines.
He took an earlier language called B (created by Thompson), improved it, added data types and structures, and called it **C**.

In **1973**, Thompson and Ritchie committed heresy: **They rewrote the Unix Kernel in C**.
Until then, it was believed that operating systems had to be written in Assembly to be fast. They proved otherwise.

**The Result: Portability**
Suddenly, Unix could move. You could take the C source code, take it to a computer with a different architecture, make small adjustments to the compiler, and recompile Unix. In a matter of weeks, you had the OS running on new hardware.
This allowed Unix to spread like a benevolent virus through all the universities in the world during the 70s.

@section: 5. The Unix Wars

During the 70s, AT&T (owner of Bell Labs) was prohibited by a US government antitrust decree from selling software or entering the computer business. They could only dedicate themselves to telephones.
So, when universities asked for Unix, AT&T sent them the magnetic tapes with the source code and said: *"Here, we only charge for the cost of the tape and shipping. It's free, but we don't give support. If it breaks, fix it yourself"*.

This created a golden age of academic collaboration. The **University of Berkeley (California)** became a massive development hub.
Berkeley students took the Unix code, improved it radically, and created their own variant: **BSD (Berkeley Software Distribution)**.
Berkeley added vital things:
*   The **TCP/IP** stack (the basis of the Internet).
*   The `vi` editor.
*   The `csh` shell.
*   Virtual memory.

### The Schism of 1984
In 1984, the US government broke up AT&T into smaller companies ("Baby Bells"). The legal restriction preventing them from selling software disappeared.
AT&T saw that Unix was gold and decided to commercialize it aggressively. They created **System V**, a corporate, closed, and expensive version.
*   They closed the source code (trade secret).
*   They started charging million-dollar licenses ($40,000 per CPU).
*   They prohibited universities from sharing the code.
*   They sued those who copied their code.

The Unix world split into a bloody civil war:
1.  **System V (AT&T):** The corporate standard.
2.  **BSD (Berkeley):** The academic and rebellious version.

Manufacturers like HP, IBM, Sun Microsystems, and Microsoft bought licenses and created their own incompatible Unixes (HP-UX, AIX, Solaris, Xenix). The dream of a standard system broke. A program written for HP-UX did not work on AIX. This fragmentation stalled innovation and left the way clear for Windows to conquer the market.

@section: 6. Richard Stallman and the Moral Crusade (GNU)

While companies fought for money, at the MIT Artificial Intelligence Laboratory, a man watched his hacker culture crumble. **Richard Stallman** (RMS) saw his colleagues being hired by companies that forced them to sign non-disclosure agreements (NDAs). Software ceased to be shared knowledge to become private property.

### The Xerox Printer Incident
Legend has it that the breaking point was a Xerox 9700 laser printer. The printer jammed constantly. Stallman wanted to modify the driver so that the printer would send a message to users warning of the jam.
He asked Xerox for the source code. They denied it: *"It's a trade secret"*.
Stallman visited a colleague at another university who had the code. He asked for it. The colleague, ashamed, said: *"I can't give it to you. I signed an NDA"*.

For Stallman, this was not a technical inconvenience; it was an ethical betrayal. He decided that proprietary software was immoral because it divided society and kept users helpless.

### The GNU Manifesto (1983)
On September 27, 1983, Stallman resigned from MIT and announced the **GNU** project (GNU's Not Unix).
His goal: To create a complete operating system, compatible with Unix, but 100% free.

He defined the **4 Freedoms of Software**:
0.  Freedom to **run** the program for any purpose.
1.  Freedom to **study** how it works and change it (Access to Source Code).
2.  Freedom to **redistribute** copies to your neighbors.
3.  Freedom to **improve** the program and release your improvements to the public.

To protect these freedoms, he created the **GPL (Copyleft)** license. Unlike Public Domain, the GPL uses copyright in reverse: *"You have the right to copy, modify, and distribute. BUT, if you distribute a modified version, **you have the obligation** to keep it free and provide the source code"*. It is a legal virus of freedom.

During the 80s, Stallman and the FSF (Free Software Foundation) worked frantically. They created industrial-quality tools that surpassed proprietary Unix ones: **GCC** (compiler), **Bash** (shell), **Emacs** (editor), **Glibc** (libraries).
By 1990, they had EVERYTHING ready... except one piece. The Kernel. Their own kernel, **Hurd**, was too complex and didn't work.

@section: 7. The Finnish Student and his "Hobby" (1991)

In Helsinki, a 21-year-old student named **Linus Torvalds** bought a new PC with an **Intel 386** processor. This chip was special: it had advanced memory management capabilities (protected mode) that allowed for true multitasking.

Linus used **MINIX**, a Unix-like operating system created by Professor **Andrew Tanenbaum** for teaching. But MINIX was deliberately limited (so students could understand it in a semester). Linus wanted to explore the real power of his 386.

He started writing a terminal emulator to connect to university servers. To save files to his hard drive, he had to write a disk driver. To manage memory, he wrote a memory manager. Without realizing it, he was writing a Kernel.

On **August 25, 1991**, he sent his famous email to Usenet (`comp.os.minix`):

> *"Hello everybody out there using minix - I'm doing a (free) operating system (just a hobby, won't be big and professional like gnu) for 386(486) AT clones..."*

He called it **Linux** (although he wanted to call it *Freax*, the FTP server administrator renamed it).

### The Tanenbaum-Torvalds Debate (1992)
In 1992, Professor Tanenbaum publicly criticized Linux in a legendary debate.
*   **Tanenbaum:** "Linux is obsolete." He argued that Linux used a **Monolithic** architecture (everything in one big block of code), which he considered a step backward compared to **Microkernels** (modern, modular, and academic).
*   **Linus:** Responded with brutal pragmatism. He said that, in theory, microkernels were nice, but in practice, they were complex and slow. "Linux gets the job done."

This discussion cemented the philosophy of Linux: **Pragmatism over theoretical purity.**

@section: 8. The Marriage of Convenience: GNU/Linux

Here occurred the chemical reaction.
1.  Linus had a functional **Kernel** but hardly any applications.
2.  Stallman had all the applications (**GNU**) but no Kernel.

Developers around the world downloaded Linus's kernel (barely a few KB), compiled it using GCC (from GNU), and ran Bash (from GNU) on top of it.
CLICK! The pieces fit perfectly.

A complete operating system was born. Although we commonly call it "Linux," the technically correct name (and for which Stallman fights tirelessly) is **GNU/Linux**, recognizing that the system is the indissoluble sum of both parts.

### The Bazaar Model
Eric S. Raymond analyzed why Linux succeeded where others (like BSD or Hurd) failed in his essay *"The Cathedral and the Bazaar"*.
*   **The Cathedral (GNU Hurd, commercial Unix):** Software is built by a select group of experts in private and released to the public when it is "perfect." It is slow and elitist.
*   **The Bazaar (Linux):** Release early, release often. Delegate everything you can. Treat your users as co-developers. "Given enough eyeballs, all bugs are shallow" (Linus's Law).

Linux proved that thousands of uncoordinated volunteers could produce better software than the biggest companies in the world.

@section: 9. The Empire Strikes Back and the Final Victory

In the 90s, Linux grew exponentially. Distributions were born (Slackware, Debian, Red Hat).
Companies like **IBM**, **Oracle**, and **HP** realized that maintaining their own Unixes (AIX, Solaris) was excessively expensive. It was cheaper to invest in Linux and share kernel development costs. In 2001, IBM invested 1 billion dollars in Linux. That legitimized the system for banks and governments.

### The Halloween Documents and SCO
Microsoft saw Linux as an existential threat. The "Halloween Documents" were leaked, internal memos where Microsoft engineers admitted that Open Source was, in the long run, superior and that they should fight it with dirty tricks ("FUD" - Fear, Uncertainty, and Doubt). Steve Ballmer called Linux "a cancer."

They indirectly funded a company called **SCO** (Santa Cruz Operation) which sued IBM and Linux users for billions, alleging that Linux contained stolen code from original Unix.
It was a years-long legal battle. The community audited the code line by line. They found nothing stolen. SCO lost, went bankrupt, and Linux emerged legally strengthened like a rock.

### The Silent Conquest
Today, the war is over. Linux has won, even if you don't see it on your desktop.
*   **Internet:** Google, Facebook, Amazon, Netflix... 99% of the cloud runs on Linux.
*   **Mobile:** Android is a Linux kernel. There are billions of devices in pockets around the world.
*   **Supercomputing:** 100% of the world's 500 most powerful supercomputers use Linux.
*   **Science:** The Large Hadron Collider, NASA, SpaceX... all use Linux.
*   **Microsoft:** Today, Microsoft is a "Platinum Member" of the Linux Foundation and sells Linux in its Azure cloud.

When you use Linux, you aren't just using a program. You are using the result of the largest human collaboration in history. You are standing on the shoulders of giants.

@quiz: What was the main philosophical difference between Linux development (the Bazaar) and commercial Unix (the Cathedral)?
@option: The Bazaar used more modern languages.
@correct: The Bazaar released code constantly and allowed anyone to contribute, while the Cathedral was closed development.
@option: The Cathedral was faster at fixing bugs.

@quiz: What legal event in the 80s caused the fragmentation of Unix and the closing of its source code?
@option: The invention of the C language.
@option: The SCO lawsuit.
@correct: The breakup of AT&T by the US government, allowing it to commercialize software.
@option: The creation of Microsoft.

@quiz: What vital component did the GNU project contribute to complete the operating system started by Linus Torvalds?
@option: The Kernel.
@option: The graphics card drivers.
@correct: The compiler (GCC), the shell (Bash), and user tools.
@option: The Windows desktop environment.

@quiz: According to Professor Tanenbaum, why was Linux "obsolete" in 1992?
@option: Because it was written in C.
@correct: Because it used a Monolithic Kernel architecture instead of Microkernel.
@option: Because it was free.