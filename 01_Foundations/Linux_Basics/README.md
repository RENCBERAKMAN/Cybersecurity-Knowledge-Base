# 🛡️ THE ARCHITECTURAL SOVEREIGNTY: LINUX KERNEL & EXPLOITATION MANIFESTO
> **"In the realm of Linux, the Kernel is the absolute truth. To conquer the system, you must understand the silence between the system calls and the logic within the binary."**

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬
[🇺🇸 ENGLISH MASTERCLASS](#-english-architectural-supremacy) | [🇹🇷 TÜRKÇE MASTERCLASS](#-türkçe-mimari-mükemmellik)
▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

## 🇺🇸 ENGLISH: ARCHITECTURAL SUPREMACY

### 🧠 I. THE KERNEL BLUEPRINT: PRIVILEGE & ABSTRACTION
Linux is not just a kernel; it's a massive abstraction layer. To exploit it, you must see through the abstraction.

#### 1. The Privilege Rings (CPU Enforcement)
* **Ring 0 (Kernel Mode):** Absolute power. The CPU allows direct execution of privileged instructions (e.g., modifying page tables, interacting with I/O ports).
* **Ring 3 (User Mode):** The sandbox. Applications are isolated. Any attempt to touch hardware directly results in a **Segmentation Fault**.
* **The Transition:** When a user-land process needs the disk, it triggers a **Trap** to the kernel via a **System Call (Syscall)**.

#### 2. The VFS (Virtual File System) & Inodes
* **Doctrine:** "Everything is a file." Whether it's a socket, a process, or a disk.
* **Inode (Index Node):** The DNA of a file. It contains the pointer to data blocks, permissions, and timestamps. It does **not** contain the filename.
* **File Descriptors (FD):** Integers (`0:stdin`, `1:stdout`, `2:stderr`) that act as handles for open files in a process.

#### 3. Memory Layout: The Hacker's Playground
* **Stack:** Grows downward. Stores local variables and return addresses. Target for **Buffer Overflows**.
* **Heap:** Grows upward. Stores dynamic memory. Target for **Use-After-Free** exploits.

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

### 🛠️ II. THE VITAL 50: COMMAND DEEP-DIVE (KERNEL & SECURITY)

| # | Command | Level | System Call / Kernel Interface | Cyber Security Application (Hacker Vision) |
| :--- | :--- | :--- | :--- | :--- |
| **1** | `ls` | Basic | `getdents64` | Identifying **Timestomping** via nanosecond timestamp analysis. |
| **2** | `cat` | Basic | `read()` / `write()` | Dumping `/etc/shadow` or private SSH keys. |
| **3** | `strace` | Expert | `ptrace()` | **THE HOLY GRAIL:** Sniffing cleartext passwords from syscalls. |
| **4** | `lsof` | Expert | `/proc` mapping | Finding hidden C2 sockets or rootkit-held file handles. |
| **5** | `find` | Mid | `stat()` on Inodes | Hunting for **SUID/SGID** binaries for Privilege Escalation. |
| **6** | `chmod` | Mid | `fchmodat()` | Setting SUID bit (`4000`) for persistence and root shells. |
| **7** | `ps` | Basic | `/proc/[PID]/stat` | Spotting **Process Hollowing** or suspicious "zombies". |
| **8** | `kill` | Mid | `kill()` (Signals) | Terminating EDR/AV agents by interrupting their heartbeat. |
| **9** | `nc` | Mid | Socket API (L4) | Creating **Reverse Shells** and raw data exfiltration. |
| **10** | `tcpdump` | Expert | `libpcap` / Raw Sockets | Sniffing unencrypted credentials on the wire. |
| **11** | `grep` | Mid | Boyer-Moore Search | Extracting API keys and passwords from massive logs. |
| **12** | `awk` | Expert | Stream Processing | Automating log parsing to find lateral movement entry points. |
| **13** | `sed` | Expert | Regex Engine | Modifying `/etc/hosts` for local MITM redirection. |
| **14** | `sudo` | Mid | `setuid(0)` | Exploiting `NOPASSWD` configurations for instant root. |
| **15** | `ssh` | Mid | Encryption Handshake | **SSH Tunneling:** Bypassing firewalls to reach internal LAN. |
| **16** | `dmesg` | Expert | Kernel Ring Buffer | Detecting VM/Sandbox environments and Kernel exploits. |
| **17** | `iptables` | Expert | Netfilter Hooks | Blocking security monitoring or setting up port forwarding. |
| **18** | `crontab` | Mid | `cron` daemon | **PERSISTENCE:** Setting up recurring reverse shells. |
| **19** | `lsmod` | Expert | `/proc/modules` | Detecting **LKM Rootkits** or kernel-level drivers. |
| **20** | `id` | Basic | `getuid()` / `getgid()` | Initial recon to check current privilege level. |
| **21** | `mount` | Mid | `mount()` | Accessing forensics images or unmounted sensitive partitions. |
| **22** | `whoami` | Basic | `geteuid()` | Confirming successful privilege escalation. |
| **23** | `ip addr` | Mid | Netlink API | Identifying network segments and rogue interfaces. |
| **24** | `journalctl`| Mid | `systemd-journald` | Post-exploitation: Clearing logs to hide malicious activity. |
| **25** | `alias` | Basic | Shell substitution | Creating **Trojans** (e.g., aliasing `ls` to run a malware). |
| **26** | `history` | Basic | `~/.bash_history` | **RECON:** Finding forgotten passwords in command history. |
| **27** | `su` | Mid | PAM Authentication | Pivoting between users (Lateral Movement). |
| **28** | `export` | Mid | Env Variables | Injecting `LD_PRELOAD` for library hijacking. |
| **29** | `chmod +s` | Expert | SUID Bit | The classic way to "leave the door open" for root access. |
| **30** | `ss` | Expert | Netlink TCP_DIAG | Detailed socket analysis for identifying data exfiltration. |
| **31** | `uname -a` | Basic | `uname()` | Finding the kernel version for specific **Kernel Exploits**. |
| **32** | `df` | Basic | `statfs()` | Monitoring disk space for massive database exfiltration. |
| **33** | `tar` | Basic | Archiving | Compressing exfiltrated data for faster transfer. |
| **34** | `curl` | Mid | Libcurl / HTTP | Downloading payloads and interacting with C2 APIs. |
| **35** | `openssl` | Expert | Cryptography | Generating rogue certs or decrypting intercepted traffic. |
| **36** | `chattr` | Expert | Ext4/XFS Attributes | Making a backdoor **Immutable** (`+i`) so even root cannot delete. |
| **37** | `ldd` | Expert | Shared Libs | Identifying dependencies for library injection attacks. |
| **38** | `netstat` | Mid | Network Stack | Legacy socket analysis for identifying beaconing activity. |
| **39** | `scp` | Mid | Secure Copy | Transferring stolen data over an encrypted channel. |
| **40** | `base64` | Mid | Encoding | Obfuscating payloads to bypass basic AV string detection. |
| **41** | `w` | Basic | `/var/run/utmp` | Checking for other logged-in admins (Avoid detection). |
| **42** | `last` | Basic | `/var/log/wtmp` | Analyzing admin login patterns for stealthy timing. |
| **43** | `top` | Basic | `/proc` Metrics | Checking for CPU spikes caused by your exploit or miner. |
| **44** | `umask` | Expert | Permission Mask | Ensuring created files are world-readable/writable. |
| **45** | `hostname` | Basic | `/etc/hostname` | Identifying the target's role (e.g., `prod-db-01`). |
| **46** | `grep -r` | Mid | Recursive Search | Searching the whole filesystem for "config", "key", "pass". |
| **47** | `tail -f` | Basic | Inotify | Real-time monitoring of logs for admin activity. |
| **48** | `insmod` | Expert | `init_module()` | Loading a custom **Kernel Rootkit** into Ring 0. |
| **49** | `systemctl` | Mid | `systemd` Manager | Disabling security services (Firewalld, Selinux). |
| **50** | `uptime` | Basic | `/proc/uptime` | Determining how long you've held the system undetected. |

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

## 🇹🇷 TÜRKÇE: MİMARİ MÜKEMMELLİK

### 🧠 I. ÇEKİRDEK (KERNEL) PLANI: YETKİ VE SOYUTLAMA
Linux sadece bir çekirdek değil; devasa bir soyutlama katmanıdır. Onu sömürmek için soyutlamanın ötesini görmelisiniz.

#### 1. Yetki Halkaları (CPU Zorlaması)
* **Ring 0 (Kernel Modu):** Mutlak güç. CPU, ayrıcalıklı talimatların (sayfa tablolarını değiştirme, I/O portlarıyla etkileşim) doğrudan yürütülmesine izin verir.
* **Ring 3 (Kullanıcı Modu):** Sandbox. Uygulamalar izoledir. Donanıma doğrudan dokunma girişimi **Segmentation Fault** ile sonuçlanır.
* **Geçiş:** Bir kullanıcı alanı işlemi diske ihtiyaç duyduğunda, bir **Sistem Çağrısı (Syscall)** üzerinden kernel'a bir **Trap** (tuzak) tetikler.

#### 2. VFS (Sanal Dosya Sistemi) ve Inode'lar
* **Doktrin:** "Her şey bir dosyadır." Soket, işlem veya disk fark etmez.
* **Inode (Index Node):** Bir dosyanın DNA'sıdır. Veri bloklarına olan işaretçiyi, izinleri ve zaman damgalarını içerir. Dosya ismini **içermez**.
* **Dosya Tanımlayıcıları (FD):** Bir işlemdeki açık dosyalar için tutamaç görevi gören tam sayılardır (`0:stdin`, `1:stdout`, `2:stderr`).

#### 3. Bellek Yapısı: Hacker'ın Oyun Alanı
* **Stack (Yığın):** Aşağı doğru büyür. Yerel değişkenleri ve dönüş adreslerini saklar. **Buffer Overflow** saldırılarının hedefidir.
* **Heap (Öbek):** Yukarı doğru büyür. Dinamik belleği saklar. **Use-After-Free** açıklarının hedefidir.

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

### 🛠️ II. HAYATİ 50: KOMUT DERİNLİĞİ (KERNEL VE GÜVENLİK)

| # | Komut | Seviye | Sistem Çağrısı / Kernel Arayüzü | Siber Güvenlik Uygulaması (Hacker Vizyonu) |
| :--- | :--- | :--- | :--- | :--- |
| **1** | `ls` | Temel | `getdents64` | Zaman damgalarını analiz ederek **Timestomping** tespiti. |
| **2** | `cat` | Temel | `read()` / `write()` | `/etc/shadow` veya SSH anahtarlarını sızdırmak. |
| **3** | `strace` | Uzman | `ptrace()` | **KUTSAL KASE:** Sistem çağrılarından parolaları ham halde çalmak. |
| **4** | `lsof` | Uzman | `/proc` haritalama | Gizli C2 soketlerini veya rootkit dosyalarını bulmak. |
| **5** | `find` | Orta | Inode üzerinde `stat()` | Yetki yükseltmek için **SUID/SGID** dosyalarını avlamak. |
| **6** | `chmod` | Orta | `fchmodat()` | Kalıcılık için bir backdoor'a SUID biti (`4000`) eklemek. |
| **7** | `ps` | Temel | `/proc/[PID]/stat` | **Process Hollowing** veya şüpheli "zombi"leri tespit etmek. |
| **8** | `kill` | Orta | `kill()` (Sinyaller) | Güvenlik (AV/EDR) ajanlarını durdurmak. |
| **9** | `nc` | Orta | Socket API (L4) | **Reverse Shell** ve veri sızdırma (Exfiltration). |
| **10** | `tcpdump` | Uzman | `libpcap` / Raw Sockets | Şifrelenmemiş kimlik bilgilerini havadan toplamak. |
| **11** | `grep` | Orta | Boyer-Moore Arama | Dev loglardan API key ve parolaları ayıklamak. |
| **12** | `awk` | Uzman | Akış İşleme | Logları parse ederek yanal hareket noktaları bulmak. |
| **13** | `sed` | Uzman | Regex Motoru | Yerel MITM için `/etc/hosts` dosyasını manipüle etmek. |
| **14** | `sudo` | Orta | `setuid(0)` | `NOPASSWD` hatalarını sömürerek anında root olmak. |
| **15** | `ssh` | Orta | Şifreleme Protokolü | **SSH Tunneling:** Firewall'u atlayıp iç ağa sızmak. |
| **16** | `dmesg` | Uzman | Kernel Ring Buffer | VM/Sandbox tespiti ve Kernel exploitleri. |
| **19** | `lsmod` | Uzman | `/proc/modules` | **LKM Rootkit**'lerini veya kernel sürücülerini tespit etmek. |
| **25** | `alias` | Temel | Shell Substitution | **Trojan** oluşturmak (Örn: `ls` komutuna malware atamak). |
| **28** | `crontab` | Orta | `cron` daemon | **KALICILIK:** Periyodik çalışan reverse shell kurmak. |
| **36** | `chattr` | Uzman | Dosya Öznitelikleri | Bir backdoor'u **Değiştirilemez** (`+i`) yaparak silinmesini engellemek. |
| **48** | `insmod` | Uzman | `init_module()` | Ring 0'a özel bir **Kernel Rootkit** yüklemek. |

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

### 🧠 III. MASTER'S PERSPECTIVE (VİZYON)
Linux'ta güç, komut ezberlemekten değil, **Veri Akışını (Data Stream)** kontrol etmekten gelir.
1.  **Terminal:** İşletim sisteminin sinir sistemine bağlanan cerrah masasıdır.
2.  **Piping (`|`):** İşlemleri kernel seviyesinde birbirine bağlayan bir veri fabrikasıdır.
3.  **Hacker Mindset:** "Bu komut ne yapar?" diye değil, "Bu komutun kernel'daki yansıması nedir ve ben bu yansımayı nasıl bükerim?" diye sormaktır.

---
**Status:** `SUPREME_MASTER_REFERENCE` | **Revision:** 3.0
**"Root is not just a user; it's the god of the machine."**