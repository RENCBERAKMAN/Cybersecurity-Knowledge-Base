<div align="center">

  <img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=260&section=header&text=NETWORK%20RECONNAISSANCE%20%26%20SCANNING&fontSize=38&animation=fadeIn&fontColor=ffffff&desc=The%20Art%20of%20Packet%20Manipulation%20%26%20Active%20Enumeration&descFontSize=18&gradient=0000FF,FF0000" />

  <br>

  <img src="https://img.shields.io/badge/PHASE-1_RECONNAISSANCE-red?style=for-the-badge&logo=radar" />
  <img src="https://img.shields.io/badge/PROTOCOL-TCP%2FIP%20STACK-blue?style=for-the-badge&logo=cisco" />
  <img src="https://img.shields.io/badge/TECHNIQUE-RAW_SOCKETS-critical?style=for-the-badge&logo=kali-linux" />
  <img src="https://img.shields.io/badge/OPSEC-STEALTH_MODE-success?style=for-the-badge&logo=tor" />
  <img src="https://img.shields.io/badge/LANG-BILINGUAL_EN_TR-orange?style=for-the-badge" />

</div>

<br>

> **"Reconnaissance is not merely finding open ports; it is the manipulation of the TCP State Machine. We do not ask the server if it is open; we inspect how it denies us. In the silence of a dropped packet, the truth resides."**

<br>

## 🧭 **NAVIGATION / NAVİGASYON**

| 🇺🇸 **[ENGLISH: TECHNICAL SUPREMACY](#-english-technical-supremacy)** | 🇹🇷 **[TÜRKÇE: MİMARİ MÜKEMMELLİK](#-türkçe-mimari-mükemmellik)** |
| :---: | :---: |
| *RFC Compliance, Raw Sockets & Packet Injection* | *RFC Uyumluluğu, Ham Soketler ve Paket Enjeksiyonu* |

<br>

<div id="english-technical-supremacy"></div>

# 🇺🇸 ENGLISH: TECHNICAL SUPREMACY

<hr style="height:4px;border-width:0;color:#0000FF;background-color:#0000FF">

### 🧠 I. THE ARCHITECTURE: PACKET PHYSICS & RFC STANDARDS
To master scanning, one must understand how the Kernel handles network stacks versus how tools like Nmap handle them via **Raw Sockets**.

#### 1. The TCP State Machine & RFC 793
* **Standard OS Behavior (SOCK_STREAM):** When you use `netcat`, the OS Kernel handles the handshake (SYN -> SYN/ACK -> ACK). This creates a full connection, logs it, and is noisy.
* **Scanner Behavior (SOCK_RAW):** Tools like Nmap bypass the OS TCP stack. They construct packets bit-by-bit.
    * **SYN Scan:** Sends SYN. Receives SYN/ACK. instead of sending ACK (which the Kernel would do), Nmap sends **RST** (Reset). This tears down the connection before the Application Layer (Layer 7) logs it.

#### 2. Fragmentation & MTU (RFC 791)
* **Mechanism:** IP packets have a Maximum Transmission Unit (MTU), usually 1500 bytes.
* **Evasion:** By splitting the TCP header (20 bytes) into tiny fragments (e.g., 8 bytes), an IDS/Firewall cannot match signatures because the "payload" is split across multiple packets. The target Kernel reassembles them, but the Firewall sees nonsense.

#### 3. Idle Scan (The Zombie Technique)
* **Concept:** Exploiting the **IP ID** field in the IP Header.
* **Logic:**
    1.  Attacker spoofs packet from a "Zombie" to the Target.
    2.  Target replies to the Zombie.
    3.  Zombie's IP ID increments.
    4.  Attacker queries Zombie's IP ID. If it jumped by 2, the Target port is OPEN. If by 1, it's CLOSED.
    * **Result:** The attacker never sends a packet to the target with their own IP.



---

### 🛠️ II. THE ARSENAL: VITAL COMMANDS & PACKET LOGIC
*Mapping the network via Raw Sockets and Protocol Manipulation.*

| # | Command | Category | Deep Technical Logic (Packet/Kernel) | 💀 Hacker Vision (Why we use this?) |
| :--- | :--- | :--- | :--- | :--- |
| **1** | `nmap -sS` | **TCP** | **SYN Stealth:** Sends SYN. On SYN/ACK, sends **RST**. Never completes 3-Way Handshake. | **Standard Op:** Avoids application-level logging (`/var/log/apache2/access.log`) while confirming open ports. |
| **2** | `nmap -sT` | **TCP** | **Connect:** Uses `connect()` syscall. Completes 3-Way Handshake. OS manages the socket. | **Legacy/Pivot:** Used when user doesn't have root privileges (cannot open Raw Sockets) or scanning via Proxychains. |
| **3** | `nmap -sU` | **UDP** | **UDP Empty:** Sends empty UDP payload. If no reply = Open/Filtered. If ICMP Type 3 Code 3 = Closed. | **The Blind Spot:** Admins monitor TCP but ignore UDP. Essential for finding DNS (53), SNMP (161), VPNs (500/4500). |
| **4** | `nmap -sA` | **TCP** | **ACK Scan:** Sends a packet with only the **ACK** flag set. Violates state. | **Firewall Mapping:** Determines if a firewall is Stateful or Stateless. If RST received = Unfiltered. No reply = Filtered. |
| **5** | `nmap -sN` | **TCP** | **Null Scan:** Sends a TCP packet with **No Flags** set (0x00). | **RFC Violation:** RFC 793 says "If port closed, send RST." If open, ignore. Bypasses simple stateless firewalls. |
| **6** | `nmap -f` | **Evasion** | **Fragmentation:** Splits IP headers into 8-byte chunks (RFC 791). | **IDS Evasion:** Slices the packet so IDS signatures (like Snort rules) fail to match the attack pattern. |
| **7** | `nmap -D RND:10` | **Evasion** | **Decoy:** Spoofs source IP. Sends frames from 10 random IPs + your IP. | **Confusion:** The admin sees 11 attackers. They cannot block all of them without causing Denial of Service. |
| **8** | `nmap --source-port 53` | **Evasion** | **Static Source Port:** Hardcodes the source port to 53 (DNS) or 80 (HTTP). | **ACL Bypass:** Poorly configured firewalls often allow *any* traffic coming *from* port 53 (assuming it's DNS reply). |
| **9** | `masscan -p80 0.0.0.0/0` | **Mass** | **Async Raw Socket:** Uses custom TCP stack. Transmits without waiting for receive. | **Internet Scale:** Scanning the *entire internet* for a specific vulnerability in under 6 minutes. |
| **10** | `hping3 -S -V -p 80` | **Packet** | **Custom TCP:** Allows manual setting of Sequence Numbers and Window Size. | **Firewall Stress Test:** Testing specific rule sets or generating deterministic TCP sequences for hijacking. |
| **11** | `netdiscover -p` | **L2** | **Passive ARP:** Listens to ARP Broadcasts (Who has IP X?) on Layer 2. | **Stealth Recon:** Mapping a local LAN without sending a *single* packet. Totally invisible to IDS. |
| **12** | `arp-scan -l` | **L2** | **Active ARP:** Sends ARP Requests to the broadcast MAC (`ff:ff:ff:ff:ff:ff`). | **L2 Enumeration:** Finding hidden devices that block ICMP (Ping) but must answer ARP to function. |
| **13** | `dig axfr @ns1.target.com` | **DNS** | **Zone Transfer:** Queries for AXFR record type. | **Data Exfil:** Downloading the entire internal network map (subdomains, internal IPs) from a misconfigured DNS. |
| **14** | `wafw00f` | **Web** | **HTTP Fingerprinting:** Sends malformed HTTP requests to trigger specific error codes. | **Defense Mapping:** Identifying if Cloudflare, Akamai, or ModSecurity is protecting the target. |
| **15** | `fping -a -g` | **ICMP** | **ICMP Sweep:** Sends ICMP Type 8 (Echo Request) in parallel. | **Live Host Check:** Quickly identifying which IPs are alive in a /24 subnet before heavy scanning. |
| **16** | `nikto -Tuning x` | **Web** | **Heuristic Scan:** Sends anti-IDS encoding (URL encoding, directory traversal). | **Web Vulnerability:** Finding default files, outdated server banners, and misconfigurations aggressively. |
| **17** | `nmap --script vuln` | **NSE** | **Lua Scripting:** Uses Nmap Scripting Engine to check specific CVEs. | **Weaponization:** Turning a scanner into an exploit launcher (e.g., checking for EternalBlue MS17-010). |
| **18** | `zmap -p 443` | **Mass** | **Cyclic Group:** Iterates over IP space using multiplicative groups modulo p. | **Research:** Performing single-packet surveys of the IPv4 address space with mathematical efficiency. |
| **19** | `nmap --mtu 24` | **Evasion** | **Custom MTU:** Manually sets Maximum Transmission Unit size. | **Firewall Evasion:** Forcing fragmentation that might bypass filters dropping standard fragmented packets. |
| **20** | `unicornscan` | **Mass** | **Asynchronous:** User-land and Kernel-land separated packet handling. | **Speed:** Legacy tool for extremely fast UDP scanning and banner grabbing. |
| **21** | `dnsenum` | **DNS** | **Brute Force:** Iterates through a dictionary of subdomains. | **Discovery:** Finding hidden dev/staging servers (`dev.corp.com`) that aren't public. |
| **22** | `snmp-check` | **UDP** | **SNMP Walk:** Queries OIDs (Object Identifiers) via UDP 161. | **Info Leak:** Extracting user lists, running processes, and routing tables from leaky printers/routers. |
| **23** | `nmap -O` | **OS** | **TCP/IP Fingerprinting:** Analyzes ISN (Initial Sequence Number) generation patterns. | **Target Profiling:** Knowing if the target is Linux 2.6 or Windows Server 2019 to select the right exploit. |
| **24** | `firewalk` | **Trace** | **TTL Expiration:** Sends TCP packets with incrementing TTL to find filtering hop. | **ACL Mapping:** Determining exactly which router hop is blocking specific traffic. |
| **25** | `ncat -l -p 443` | **Net** | **Listen Mode:** Opens a socket in Listen state. | **Data Exfil/C2:** Testing egress filtering or setting up a simple listener for a reverse shell. |

<br><br>

<div id="türkçe-mimari-mükemmellik"></div>

# 🇹🇷 TÜRKÇE: MİMARİ MÜKEMMELLİK

<hr style="height:4px;border-width:0;color:#FF0000;background-color:#FF0000">

### 🧠 I. MİMARİ: PAKET FİZİĞİ VE RFC STANDARTLARI
Taramada ustalaşmak için, Kernel'ın ağ yığınlarını nasıl yönettiği ile araçların (Nmap gibi) **Ham Soketler (Raw Sockets)** üzerinden bunu nasıl manipüle ettiğini anlamak gerekir.

#### 1. TCP Durum Makinesi ve RFC 793
* **Standart OS Davranışı (SOCK_STREAM):** `netcat` kullandığınızda, el sıkışmayı (Handshake) Kernel yönetir (SYN -> SYN/ACK -> ACK). Bu tam bir bağlantı oluşturur ve gürültülüdür (log bırakır).
* **Tarayıcı Davranışı (SOCK_RAW):** Nmap gibi araçlar OS TCP yığınını baypas eder. Paketleri bit bit kendileri oluşturur.
    * **SYN Taraması:** SYN gönderir. SYN/ACK alır. Kernel'ın yapacağı gibi ACK göndermek yerine, Nmap **RST** (Reset) gönderir. Bu, Uygulama Katmanı (Layer 7) loglamadan bağlantıyı yıkar.

#### 2. Parçalama (Fragmentation) ve MTU (RFC 791)
* **Mekanizma:** IP paketlerinin bir Maksimum İletim Birimi (MTU) vardır, genelde 1500 byte.
* **Atlatma (Evasion):** TCP başlığı (20 byte) çok küçük parçalara (örn: 8 byte) bölünürse, IDS/Firewall imzaları eşleştiremez. Hedef Kernel bunları birleştirir ama Güvenlik Duvarı anlamsız parçalar görür.

#### 3. Idle (Zombi) Taraması
* **Kavram:** IP Başlığındaki **IP ID** alanını sömürmek.
* **Mantık:**
    1.  Saldırgan, bir "Zombi" makineden Hedefe sahte (spoofed) paket atar.
    2.  Hedef, Zombi'ye cevap verir.
    3.  Zombi'nin IP ID'si artar.
    4.  Saldırgan Zombi'nin IP ID'sini sorgular. Eğer 2 arttıysa Hedef port AÇIKTIR. 1 arttıysa KAPALIDIR.
    * **Sonuç:** Saldırgan hedefe kendi IP'siyle asla paket göndermez.



---

### 🛠️ II. CEPHANELİK: HAYATİ KOMUTLAR VE PAKET MANTIĞI
*Ham Soketler ve Protokol Manipülasyonu ile Ağı Haritalamak.*

| # | Komut | Kategori | Derin Teknik Mantık (Paket/Kernel) | 💀 Hacker Vizyonu (Neden kullanıyoruz?) |
| :--- | :--- | :--- | :--- | :--- |
| **1** | `nmap -sS` | **TCP** | **SYN Gizli:** SYN atar. SYN/ACK gelince **RST** atar. 3'lü El Sıkışma asla tamamlanmaz. | **Standart Op:** Uygulama seviyesinde loglanmadan (`apache2/access.log`) açık portları doğrulamak. |
| **2** | `nmap -sT` | **TCP** | **Connect:** `connect()` syscall kullanır. El Sıkışmayı tamamlar. Soketi OS yönetir. | **Eski/Pivot:** Root yetkisi yoksa (Raw Socket açılamıyorsa) veya Proxychains üzerinden tarama yaparken kullanılır. |
| **3** | `nmap -sU` | **UDP** | **UDP Boş:** Boş UDP yükü atar. Cevap yoksa = Açık/Filtreli. ICMP Type 3 Code 3 gelirse = Kapalı. | **Kör Nokta:** Adminler TCP'yi izler ama UDP'yi unutur. DNS (53), SNMP (161), VPN (500) bulmak için şarttır. |
| **4** | `nmap -sA` | **TCP** | **ACK Tarama:** Sadece **ACK** bayrağı set edilmiş paket atar. Durum ihlali yaratır. | **Firewall Haritalama:** Firewall'un "Durumlu" (Stateful) mu "Durumsuz" mu olduğunu anlar. RST gelirse filtre yok demektir. |
| **5** | `nmap -sN` | **TCP** | **Null Tarama:** **Hiçbir Bayrağı** olmayan (0x00) paket atar. | **RFC İhlali:** RFC 793 "Port kapalıysa RST dön" der. Açıksa cevap dönmez. Basit firewall'ları atlatır. |
| **6** | `nmap -f` | **Evasion** | **Parçalama:** IP başlıklarını 8-byte'lık parçalara böler (RFC 791). | **IDS Atlatma:** Paketi dilimleyerek IDS imzalarının (Snort kuralları gibi) saldırı desenini yakalamasını engeller. |
| **7** | `nmap -D RND:10` | **Evasion** | **Yemleme:** Kaynak IP sahteciliği. Sizin IP'niz + 10 Rastgele IP'den paket gider. | **Karmaşa:** Admin 11 saldırgan görür. Hepsini engellerse servisi kendi eliyle kesmiş olur (DoS). |
| **8** | `nmap --source-port 53` | **Evasion** | **Statik Kaynak Port:** Kaynak portunu 53 (DNS) veya 80 (HTTP) olarak sabitler. | **ACL Bypass:** Kötü yapılandırılmış firewall'lar, kaynak portu 53 olan trafiğe (DNS cevabı sanarak) güvenir. |
| **9** | `masscan -p80 0.0.0.0/0` | **Kitle** | **Asenkron Ham Soket:** Özel TCP yığını kullanır. Cevabı beklemeden sürekli iletim yapar. | **İnternet Ölçeği:** Belirli bir zafiyet için *tüm interneti* 6 dakikadan kısa sürede taramak. |
| **10** | `hping3 -S -V -p 80` | **Paket** | **Özel TCP:** Sıra Numaraları (SEQ) ve Pencere Boyutunu manuel ayarlamayı sağlar. | **Firewall Stres Testi:** Belirli kural setlerini test etmek veya Hijacking için tahmin edilebilir TCP dizileri üretmek. |
| **11** | `netdiscover -p` | **L2** | **Pasif ARP:** Layer 2'deki ARP Yayınlarını (IP X kimde?) sadece dinler. | **Gizli Keşif:** Tek bir paket bile göndermeden yerel ağı haritalamak. IDS için tamamen görünmezdir. |
| **12** | `arp-scan -l` | **L2** | **Aktif ARP:** Yayın MAC adresine (`ff:ff:ff:ff:ff:ff`) ARP İsteği gönderir. | **L2 Numaralandırma:** ICMP (Ping) engelleyen ama çalışmak için ARP cevaplamak zorunda olan gizli cihazları bulmak. |
| **13** | `dig axfr @ns1.target.com` | **DNS** | **Bölge Transferi:** AXFR kayıt türünü sorgular. | **Veri Sızdırma:** Tüm iç ağ haritasını (alt domainler, iç IP'ler) yanlış yapılandırılmış DNS'ten indirmek. |
| **14** | `wafw00f` | **Web** | **HTTP Parmak İzi:** Özel hata kodlarını tetiklemek için bozuk HTTP istekleri atar. | **Savunma Haritalama:** Hedefi Cloudflare, Akamai veya ModSecurity'nin koruyup korumadığını anlamak. |
| **15** | `fping -a -g` | **ICMP** | **ICMP Süpürme:** ICMP Type 8 (Echo Request) paketlerini paralel gönderir. | **Canlı Host Kontrolü:** Ağır taramaya başlamadan önce /24 subnetinde hangi IP'lerin canlı olduğunu hızla bulmak. |
| **16** | `nikto -Tuning x` | **Web** | **Sezgisel Tarama:** Anti-IDS kodlamaları (URL encoding, dizin atlatma) gönderir. | **Web Zafiyeti:** Varsayılan dosyaları, eski sunucu bannerlarını ve konfigürasyon hatalarını agresifçe bulmak. |
| **17** | `nmap --script vuln` | **NSE** | **Lua Scripting:** Belirli CVE'leri kontrol etmek için Nmap Script Motorunu kullanır. | **Silahlaştırma:** Tarayıcıyı bir exploit fırlatıcısına dönüştürmek (örn: EternalBlue MS17-010 kontrolü). |
| **18** | `zmap -p 443` | **Kitle** | **Döngüsel Grup:** Modulo p çarpımsal gruplarını kullanarak IP uzayını tarar. | **Araştırma:** Matematiksel verimlilikle IPv4 uzayında tek paketlik anketler yapmak. |
| **19** | `nmap --mtu 24` | **Evasion** | **Özel MTU:** Maksimum İletim Birimi boyutunu manuel ayarlar. | **Firewall Atlatma:** Standart parçalanmış paketleri düşüren filtreleri aşmak için parçalamayı zorlamak. |
| **20** | `unicornscan` | **Kitle** | **Asenkron:** User-land ve Kernel-land paket işlemlerini ayırır. | **Hız:** Aşırı hızlı UDP taraması ve banner yakalama için kullanılan efsanevi araç. |
| **21** | `dnsenum` | **DNS** | **Kaba Kuvvet:** Bir sözlük dosyasındaki alt domainleri dener. | **Keşif:** Halka açık olmayan gizli geliştirme sunucularını (`dev.corp.com`) bulmak. |
| **22** | `snmp-check` | **UDP** | **SNMP Yürüyüşü:** UDP 161 üzerinden OID (Nesne Tanımlayıcı) sorgular. | **Bilgi Sızıntısı:** Yazıcılardan/Routerlardan kullanıcı listesi ve yönlendirme tablolarını çekmek. |
| **23** | `nmap -O` | **OS** | **TCP/IP Parmak İzi:** ISN (İlk Sıra Numarası) üretim desenlerini analiz eder. | **Hedef Profilleme:** Doğru exploit'i seçmek için hedefin Linux 2.6 mı Windows Server 2019 mu olduğunu bilmek. |
| **24** | `firewalk` | **Trace** | **TTL Bitişi:** Filtreleyen hop noktasını bulmak için artan TTL ile TCP atar. | **ACL Haritalama:** Hangi router'ın trafiği tam olarak nerede engellediğini belirlemek. |
| **25** | `ncat -l -p 443` | **Ağ** | **Dinleme Modu:** Listen (Dinleme) durumunda bir soket açar. | **Veri Sızdırma/C2:** Egress (Çıkış) filtrelerini test etmek veya reverse shell için basit bir dinleyici kurmak. |

<br>

<div align="center">
  <img src="https://capsule-render.vercel.app/render?type=soft&color=gradient&height=100&section=footer&text=RECON%20IS%20NOT%20A%20STEP%20IT%20IS%20THE%20FOUNDATION&fontSize=22&animation=fadeIn&fontColor=ffffff&gradient=0000FF,FF0000" />
  <br>
  <i>"We observe, we orient, we decide, we act."</i>
</div>