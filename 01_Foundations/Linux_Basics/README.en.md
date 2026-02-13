<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=LINUX%20BASICS%20MASTERCLASS&fontSize=42&animation=fadeIn&fontColor=ffffff&desc=From%20Zero%20to%20OS%20Mastery%20and%20Hacker%20Vision&descFontSize=20&gradient=000000,00FF00" />

<br>

<img src="https://img.shields.io/badge/DOMAIN-SYSTEM_ARCHITECTURE-black?style=for-the-badge&logo=linux" />
<img src="https://img.shields.io/badge/LEVEL-FOUNDATION_TO_MASTERY-red?style=for-the-badge&logo=hackthebox" />
<img src="https://img.shields.io/badge/FOCUS-OFFENSIVE_BASICS-blue?style=for-the-badge&logo=bash" />
<img src="https://img.shields.io/badge/LANG-ENGLISH-success?style=for-the-badge&logo=us" />

</div>

<br>

> **"Linux is not an operating system; it is a philosophy. Novices memorize commands; masters understand how the system breathes, how processes dance in memory, and how permissions can be bent. He who does not know the foundations can never conquer the summit."**

---

## 🧭 NAVIGATION

| [1. Core Philosophy: Everything is a File](#1-core-philosophy-everything-is-a-file) | [2. File System Hierarchy (FHS)](#2-file-system-hierarchy-tactical-map) | [3. Permissions and SUID Doctrine](#3-permissions-and-suid-doctrine-dac-architecture) |
| :---: | :---: | :---: |
| [4. Process Anatomy and Management](#4-process-anatomy-and-management) | [5. I/O Redirection and Pipes](#5-io-redirection-and-pipes) | [6. Environment and Shell Architecture](#6-environment-and-shell-architecture) |

---

<div id="1-core-philosophy-everything-is-a-file"></div>

## 🧠 1. CORE PHILOSOPHY: EVERYTHING IS A FILE

The biggest feature that distinguishes Linux from other systems (like Windows) is the **"Everything is a file"** logic. This is not a metaphor, but a technical architecture.

### Atomic Analysis: VFS (Virtual File System)
Linux uses VFS to manage hardware and software through the same interface.
* **Text File:** Is a file.
* **Hard Drive (e.g., `/dev/sda`):** Is a file. If you `write()` to it, you write to the physical disk.
* **Network Socket:** Is a file. Sending data to a remote computer is just a `write()` to a network socket file.
* **Processes:** Are files under the `/proc` directory.

**Hacker Perspective:** If everything in a system is a file, taking over the system means; **finding the right file and having the right read/write (r/w) permissions**. The terminal is just a text interface that interacts with these files.

---

<div id="2-file-system-hierarchy-tactical-map"></div>

## 🗺️ 2. FILE SYSTEM HIERARCHY: TACTICAL MAP

Forget the `C:\` or `D:\` logic in Windows. In Linux, there is a single root: `/`. All disks, USB drives, and network drives are mounted under this root.

| Directory | Atomic Function | 💀 Attacker / Defender Vision |
| :--- | :--- | :--- |
| `/` | **Root:** The starting point of everything in the system. | Only the `root` user can write here. |
| `/bin` & `/sbin` | **Binaries:** Basic system commands (`ls`, `cat`, `ip`). `sbin` contains admin commands. | Malware can replace the commands here with their own malicious versions (Binary Hijacking). |
| `/etc` | **Etcetera:** All system configuration files are here. Contains no binaries. | **Gold Mine:** Password hashes (`/etc/shadow`), network settings, service configurations. First place to look. |
| `/home` & `/root` | **User Directories:** Personal files, hidden configurations (`.bashrc`). | Stealing private keys (`id_rsa`) in the hidden `.ssh` folder and forgotten passwords in `.bash_history`. |
| `/tmp` | **Temporary:** Temporary files. Deleted on every reboot. | **Safe Haven:** Every user on the system has write permission here (`rwxrwxrwt`). Malicious payloads and Exploits are downloaded here first. |
| `/var` | **Variable:** Constantly changing files. Logs (`/var/log`), web server files (`/var/www`). | Where logs are destroyed to cover tracks (`rm -rf /var/log/*`) or where exfiltrated data is stored. |
| `/dev` | **Devices:** Hardware devices (`/dev/null`, `/dev/sda`). | Using `/dev/zero` to destroy data or `/dev/urandom` to generate random encryption keys. |
| `/proc` | **Process:** A virtual directory that doesn't actually take up disk space, where the Kernel shows RAM as files. | Reading memory dumps of all running applications, extracting passwords from RAM (Memory Scraping). |



---

<div id="3-permissions-and-suid-doctrine-dac-architecture"></div>

## 🔐 3. PERMISSIONS AND SUID DOCTRINE (DAC ARCHITECTURE)

Linux security is built on the DAC (Discretionary Access Control) model. A file has 3 types of owners: **User (u)**, **Group (g)**, and **Others (o)**. Each has 3 rights: **Read (r)**, **Write (w)**, **Execute (x)**.

### The Mathematical Logic of Permissions
Permissions are usually expressed in the Octal number system:
* `4` = **Read (r)** * `2` = **Write (w)** (Write / Modify)
* `1` = **Execute (x)** (Execute / Enter directory)

Example: `chmod 755 script.sh`
* `7 (4+2+1)`: File owner reads, writes, executes.
* `5 (4+1)`: Group members read and execute (cannot write).
* `5 (4+1)`: Everyone else reads and executes.

### SUID (Set Owner User ID) Bit: The Key to Privilege Escalation
Normally, when you execute a file, the file runs with **your** privileges. However, if the SUID bit (`s` or `4000`) is set, the file runs with the privileges of **the file's owner** (usually root).

* **Why does it exist?** For a regular user to change their own password, they must run the `/usr/bin/passwd` command. This command writes to the `/etc/shadow` file (which only root can write to). Therefore, the `passwd` command has the SUID bit; when executed, it momentarily elevates to `root` privileges.
* **Hacker Vision:** If you want to elevate privileges (become Root) on a system, you look for commands that have been accidentally assigned the SUID bit (`find / -perm -4000 2>/dev/null`). If this bit is on `nmap`, `vim`, `python`, or `bash`, you can instantly get a root shell by utilizing the features of these tools (GTFOBins methodology).

---

<div id="4-process-anatomy-and-management"></div>

## ⚙️ 4. PROCESS ANATOMY AND MANAGEMENT

Every running program in Linux is a Process and is managed by the Kernel.

### Lifecycle: Fork() and Execve()
A new process is not created from scratch in Linux. An existing process copies itself (`fork`), and then the new program is loaded into this copy (`execve`).
* Every process has a **PID** (Process ID) and its creator parent has a **PPID** (Parent Process ID).
* When the system boots, `PID 1` (Init/Systemd) starts, and everything else descends from it.

### Talking with Signals
To talk to processes, Kernel signals are used, not the keyboard. The `kill` command doesn't actually kill a program; it sends a signal to it.
* `SIGTERM (15)`: Tells the application "Please close". The application saves its data and closes (Graceful).
* `SIGKILL (9)`: The Kernel instantly wipes the application from memory. The application has no right to object (Forceful).
* `SIGSTOP (19)`: Freezes the application (Sends it to the background `Ctrl+Z`).

**Hacker Vision:** When a reverse shell running silently in the background (`Daemon`) is caught, looking for orphaned processes with a PPID of `1` or hidden processes is the most fundamental way to detect Indicators of Compromise (IoC).

---

<div id="5-io-redirection-and-pipes"></div>

## 🔀 5. I/O REDIRECTION AND PIPES

Commands typed in the terminal don't magically appear on the screen. Linux opens 3 Standard Data Streams for every process:

1.  **STDIN (0):** Standard Input (Usually Keyboard)
2.  **STDOUT (1):** Standard Output (Successful result printed on screen)
3.  **STDERR (2):** Standard Error (Error message printed on screen)

### Redirection
* `>` : Writes the output to a file (Overwrites the file). Ex: `echo "hack" > file.txt`
* `>>` : Appends the output to the end of the file (Does not overwrite).
* `2>/dev/null` : Sends error messages (STDERR=2) to Linux's black hole (`/dev/null`) to prevent cluttering the screen. (Ex: To hide files with permission denied errors).

### Pipes (`|`)
Takes the STDOUT of one program and makes it the STDIN of another. It is the heart of the Unix philosophy of Linux: *"Make each program do one thing well."*

* **Attacker Example:** To find users on the system who can use a bash shell:
  ```bash
  cat /etc/passwd | grep "/bin/bash" | awk -F: '{print $1}'