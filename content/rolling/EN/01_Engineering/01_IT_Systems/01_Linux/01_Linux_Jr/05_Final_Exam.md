
@title: Certification Exam: Linux Operator Jr.
@exam
@icon: ⚔️
@description: Demonstrate your terminal mastery. Pass this intensive 40-question challenge to obtain the Linux Operator Jr. certificate.
@order: 5

# The Final Challenge: Linux Operator Jr.

You have come a long way. From understanding what the Kernel is to writing your own scripts, through permission and process management.

The time has come to prove your worth. This exam covers all vital concepts learned in the previous 4 modules.

> **Instructions:** Select the single correct answer for each question. You must pass the majority to clear the test and obtain the node certification.


## Block 1: Architecture and Fundamentals

@quiz: Technically speaking, what is "Linux"?
@option: A complete operating system with graphical environment.
@correct: The Kernel (core) of the operating system.
@option: A free software distribution.
@option: A desktop environment similar to Windows.

@quiz: What is a Linux "Distribution" (Distro)?
@option: A pirated version of Windows.
@option: The Kernel source code without compiling.
@correct: A bundle including the Kernel, GNU tools, graphical environment, and package manager.
@option: A program to distribute files over a network.

@quiz: In the Linux directory structure, what symbol represents the root of the file system?
@option: \ (Backslash)
@option: C:
@correct: / (Forward slash)
@option: ~ (Tilde)

@quiz: What feature defines the `/tmp` folder?
@option: It stores temporary files forever.
@correct: Its content is automatically deleted upon system reboot.
@option: Only the root user can write to it.
@option: It contains temporary hardware drivers.

@quiz: In which directory are global system configuration files stored?
@option: /home
@option: /bin
@correct: /etc
@option: /var

@quiz: Which directory contains special files representing hardware devices (like hard drives)?
@option: /dev
@correct: /dev
@option: /mnt
@option: /media
@option: /sys

@quiz: What is the process with PID 1, responsible for starting other services in modern systems?
@option: bash
@option: kernel
@correct: systemd (or init)
@option: grub

@quiz: What is "User Space"?
@option: The /home folder where users live.
@option: The RAM reserved for the graphics card.
@correct: The memory area where normal applications run, without direct hardware access.
@option: The free space on the hard drive.

@quiz: What is the function of the SWAP partition or file?
@option: To store system boot (Bootloader).
@correct: To act as virtual memory when RAM fills up.
@option: To save automatic backups.
@option: To accelerate internet connection.

@quiz: What does it mean for software to be "Open Source"?
@option: That it is free (free as in beer).
@correct: That its source code is accessible, modifiable, and redistributable (free as in speech).
@option: That it has no copyright.
@option: That it only works on Linux systems.


## Block 2: The Terminal and Files

@quiz: You are in `/home/user/documents` and want to go up one level to `/home/user`. What command do you use?
@option: cd .
@correct: cd ..
@option: cd /
@option: cd ~

@quiz: What does the `pwd` command do?
@option: Changes your password.
@correct: Shows the full path of the directory you are in (Print Working Directory).
@option: Shows running processes.
@option: Turns off the computer (Power Down).

@quiz: Which command would you use to rename the file `photo.jpg` to `image.jpg`?
@option: cp photo.jpg image.jpg
@option: ren photo.jpg image.jpg
@correct: mv photo.jpg image.jpg
@option: rm photo.jpg image.jpg

@quiz: What is the `touch` command used for if the file already exists?
@option: Deletes the file content.
@correct: Updates the file's modification timestamp to the current time.
@option: Creates a copy of the file.
@option: Opens the file for editing.

@quiz: You executed `ls` and don't see files starting with a dot (e.g., `.bashrc`). What option do you need?
@option: ls -l
@correct: ls -a
@option: ls -h
@option: ls -R

@quiz: How would you create a nested directory structure `project/src/img` with a single command?
@option: mkdir project/src/img
@correct: mkdir -p project/src/img
@option: mkdir -r project/src/img
@option: touch project/src/img

@quiz: Which command is extremely dangerous and will delete everything without asking or recovery possibility?
@option: rm -i file
@option: rmdir folder
@correct: rm -rf /
@option: delete all

@quiz: You need to know what type of content a file named `data` (with no extension) has. What do you use?
@option: cat data
@option: ls -l data
@correct: file data
@option: type data

@quiz: You need to find all files ending in `.conf` inside the `/etc` folder.
@option: grep -r ".conf" /etc
@correct: find /etc -name "*.conf"
@option: locate /etc
@option: search .conf

@quiz: What is the main difference between `cat` and `less`?
@option: `cat` is for editing and `less` is for reading.
@correct: `cat` shows everything at once, `less` allows navigating the file by paging.
@option: `less` is an old version of `cat`.
@option: `cat` only works with small files.


## Block 3: Administration and Permissions

@quiz: If a file has `755` permissions (rwxr-xr-x), what can the "Group" do?
@option: Read, Write, and Execute.
@correct: Read and Execute, but not Write.
@option: Read Only.
@option: Nothing.

@quiz: What numeric (octal) value represents "Read and Write" permission (rw-)?
@option: 7
@correct: 6
@option: 5
@option: 4

@quiz: Which command do you use to change the owner of a file?
@option: chmod
@correct: chown
@option: chgrp
@option: passwd

@quiz: What is the `sudo` command used for?
@option: To change a user's password.
@correct: To execute a command with administrator (root) privileges.
@option: To suspend the computer.
@option: To uninstall programs.

@quiz: In which file are user passwords stored in encrypted form?
@option: /etc/passwd
@option: /etc/security
@correct: /etc/shadow
@option: /var/log/auth.log

@quiz: What is the UID (User ID) of the root user?
@option: 1000
@option: 1
@correct: 0
@option: -1

@quiz: You have a stuck process that doesn't respond to a normal `kill`. What signal do you use to force its immediate destruction?
@option: -15 (SIGTERM)
@option: -1 (SIGHUP)
@correct: -9 (SIGKILL)
@option: -2 (SIGINT)

@quiz: Which command tells you which user you currently are in the terminal?
@option: whoareyou
@correct: whoami
@option: pwd
@option: w

@quiz: Which command allows you to see processes and resource consumption in real time?
@option: ps aux
@correct: top (or htop)
@option: df -h
@option: free -m

@quiz: Which file must you edit (using `visudo`) to configure who can use `sudo`?
@option: /etc/passwd
@option: /etc/admin
@correct: /etc/sudoers
@option: /etc/group


## Block 4: Networking and Scripting

@quiz: Which command would you use to see your assigned IP address?
@option: ip address (or ip addr)
@option: ping localhost
@option: netstat
@correct: ip addr (or ip a)

@quiz: If you `ping google.com` and it fails, but `ping 8.8.8.8` works, what is failing?
@option: Your network card.
@correct: The DNS server (Name resolution).
@option: The network cable.
@option: Google's server.

@quiz: You want to install the `git` package on an Ubuntu/Debian system. What is the recommended first step before installing?
@correct: sudo apt update
@option: sudo apt install git
@option: sudo apt upgrade
@option: sudo apt remove git

@quiz: What does the command `grep` do?
@option: Downloads files from the internet.
@correct: Searches for text or patterns inside files or data streams.
@option: Groups files into folders.
@option: Shows RAM usage.

@quiz: What does the first line of a script `#!/bin/bash` mean?
@option: It is a comment and is ignored.
@correct: It is the Shebang, indicates which interpreter must execute the script.
@option: Defines the path where the script will be saved.
@option: Gives execution permissions to the script.

@quiz: To execute a script named `my_script.sh` in the current folder, which command is correct?
@option: my_script.sh
@correct: ./my_script.sh
@option: run my_script.sh
@option: call my_script.sh

@quiz: In a script, how do you access the value of a variable named `NAME`?
@option: NAME
@correct: $NAME
@option: %NAME%
@option: &NAME

@quiz: What exit code indicates that a command executed successfully?
@option: 1
@option: -1
@correct: 0
@option: 100

@quiz: Which redirection operator is used to add text to the end of a file without deleting its current content?
@option: >
@correct: >>
@option: <
@option: |

@quiz: What is the key combination to stop (kill) a program running in the terminal (SIGINT)?
@option: Ctrl + Z
@correct: Ctrl + C
@option: Ctrl + D
@option: Alt + F4
