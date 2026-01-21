<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=NETWORK%20ATTACK%20DOCTRINE&fontSize=42&animation=fadeIn&fontColor=ffffff&desc=Atomic%20Logic%20to%20Advanced%20Exploitation&descFontSize=20&gradient=FF0000,00008B" />

<br>

<img src="https://img.shields.io/badge/PHASE-EXPLOITATION-red?style=for-the-badge&logo=target" />
<img src="https://img.shields.io/badge/SECURITY-TOP_SECRET-black?style=for-the-badge&logo=hackthebox" />
<img src="https://img.shields.io/badge/DOMAIN-OFFENSIVE_INFRASTRUCTURE-blue?style=for-the-badge&logo=linux" />
<img src="https://img.shields.io/badge/LAYER-2_THRU_4-orange?style=for-the-badge&logo=wireshark" />

</div>

<br>

> **"A network is a consensus of lies that machines agree to call the truth. To attack a network is to introduce a new lie so convincing that the machines have no choice but to obey it. We do not break the wire; we redefine the reality of the wire."**

<br>

## 🧭 NAVIGATION

| 🇺🇸 **ENGLISH: TECHNICAL SUPREMACY** |
| :--- |
| 1. [Conceptual Foundation: The Trust Paradox](#1-conceptual-foundation-the-trust-paradox) |
| 2. [System Architecture: Kernel vs. The Wire](#2-system-architecture-kernel-vs-the-wire) |
| 3. [Protocol & State Logic: The Abused Handshake](#3-protocol--state-logic-the-abused-handshake) |
| 4. [Where Weakness Emerges: Structural Trust](#4-where-weakness-emerges-structural-trust) |
| 5. [The Supreme Arsenal (Advanced Attack Table)](#-the-supreme-arsenal) |

<br>

| 🇹🇷 **TÜRKÇE: TEKNİK MÜKEMMELLİK** |
| :--- |
| 1. [Kavramsal Temel: Güven Paradoksu](#1-kavramsal-temel-g%C3%BCven-paradoksu) |
| 2. [Sistem Mimarisi: Kernel vs. Kablo](#2-sistem-mimarisi-kernel-vs-kablo) |
| 3. [Protokol ve Durum Mantığı: İstismar Edilen El Sıkışma](#3-protokol-ve-durum-mant%C4%B1%C4%9F%C4%B1-istismar-edilen-el-s%C4%B1k%C4%B1%C5%9Fma) |
| 4. [Zayıflığın Doğuşu: Yapısal Güven](#4-zay%C4%B1fl%C4%B1%C4%9F%C4%B1n-do%C4%9Fu%C5%9Fu-yap%C4%B1sal-g%C3%BCven) |
| 5. [Yüce Cephanelik (İleri Düzey Saldırı Tablosu)](#-y%C3%BCce-cephanelik) |

<br>

---

<div id="english-technical-supremacy"></div>

# 🇺🇸 ENGLISH: TECHNICAL SUPREMACY

### 🧠 1. CONCEPTUAL FOUNDATION: THE TRUST PARADOX
In the early days of the ARPANET, connectivity was a luxury and users were trusted academics. Protocols were designed for **efficiency and delivery**, not for **adversarial resistance**. This created the **Trust Paradox**: For a network to function, nodes must trust the identity announcements of their neighbors.

* **The Flaw of Inherent Trust:** In Layer 2 (Data Link), we trust that a MAC address belongs to the IP it claims to represent. In Layer 3 (Network), we trust that a packet's source IP is legitimate. 
* **The Hacker's Objective:** Offensive security is the practice of exploiting this "blind trust" by injecting malformed or dishonest metadata that forces the target system into an unintended state.

### ⚙️ 2. SYSTEM ARCHITECTURE: KERNEL VS. THE WIRE
Network attacks are a battle over system resources, primarily within the **OS Kernel**.

* **Netfilter and Hooks:** In Linux, every packet passes through the Netfilter framework. An attack (like a SYN Flood) forces the kernel to spend CPU cycles evaluating rules for millions of garbage packets.
* **Context Switching & IRQs:** When packets arrive at extreme speeds, the CPU is forced into a state of **Interrupt Storm**. It stops processing applications (Userland) to handle Hardware Interrupts (Kernel), leading to total system collapse.
* **The sk_buff Structure:** Each packet occupies a kernel memory structure called `sk_buff`. By sending many small packets, we can exhaust the kernel's **Slab Allocator** memory, causing a crash even if the bandwidth is low.



### 💀 3. PROTOCOL & STATE LOGIC: THE ABUSED HANDSHAKE
The TCP/IP stack is a **Deterministic State Machine**. Every state (SYN_RECV, ESTABLISHED, FIN_WAIT) has a specific set of rules.

* **Sequence Number Hijacking:** TCP uses 32-bit Sequence (SEQ) and Acknowledgment (ACK) numbers to track data. If an attacker can predict these numbers (often due to low entropy in the Kernel's PRNG), they can inject a "Command" packet into an existing session.
* **The Backlog Queue:** When a server receives a SYN, it puts the connection in the `SYN_RECV` state and stores it in the **Listen Backlog**. By never sending the final ACK, we fill this queue, preventing the server from accepting new, legitimate connections.



### 4. WHERE WEAKNESS EMERGES: STRUCTURAL TRUST
Weakness is the byproduct of **Design Trade-offs**:

1.  **ARP (Address Resolution Protocol):** Designed without a "Verification" phase. Anyone can send an ARP reply even if no one asked. This allows for **Man-in-the-Middle (MitM)** positions.
2.  **BGP (Border Gateway Protocol):** The "Internet's Glue." It trusts any router that says "I have the best path to Google's IP." This allows for **BGP Hijacking** where entire countries can lose internet access.
3.  **Lack of Authentication:** Protocols like DNS and DHCP were not designed with signatures. Whoever responds first is believed. This "Race Condition" is the foundation of most redirection attacks.



---

# 🛠️ THE SUPREME ARSENAL (ADVANCED ATTACK TABLE)
*A comprehensive guide to the most lethal network exploitation techniques.*

| Technique / Tool | Atomic Mechanism | Educational Explanation (Logic) | 💀 Strategic Adversary Objective |
| :--- | :--- | :--- | :--- |
| **ARP Spoofing** | Gratuitous ARP (Opcode 2) injection. | Redirects traffic at Layer 2 by lying about which MAC address owns an IP. | **MitM:** Intercepting cleartext traffic (HTTP/FTP/Telnet) to steal credentials. |
| **SYN Flood** | Exhaustion of the `tcp_max_syn_backlog`. | Fills the server's "half-open" connection memory so it can't talk to anyone else. | **Denial of Service:** Taking down a high-availability server with very low bandwidth. |
| **Slowloris** | HTTP Header incompleteness. | Holds HTTP sockets open by sending headers infinitely slowly. | **Application DoS:** Killing a web server (Apache/Nginx) by using up all available worker threads. |
| **DNS Cache Poisoning** | Transaction ID (TXID) Brute-forcing. | Injects a fake DNS record into a recursive resolver's cache. | **Traffic Redirection:** Sending users to a fake phishing site by hijacking their domain resolution. |
| **DHCP Starvation** | MAC Address Spoofing (DHCP Discover). | Requests every available IP in the DHCP pool until none are left. | **Network Denial:** Preventing any new legitimate device from getting an IP and joining the network. |
| **MAC Flooding** | CAM Table Overflow. | Floods a switch with random MACs until the lookup table is full. | **Fail-Open:** Forcing a switch to act like a hub, broadcasting all traffic to every port for easy sniffing. |
| **VLAN Hopping** | DTP (Dynamic Trunking Protocol) Abuse. | Negotiates a trunk link with a switch to access restricted VLANs. | **Privilege Escalation:** Jumping from a "Guest" network to the "Management" or "Server" network. |
| **STP Root Takeover** | Bridge Protocol Data Unit (BPDU) injection. | Sends a BPDU with a priority of 0 to become the center of the network. | **Traffic Grooming:** Forcing all network traffic to pass through the attacker's switch for analysis. |
| **SSL Stripping** | Proxy-based 302 Redirection. | Downgrades an HTTPS request to HTTP before the browser can encrypt it. | **Bypassing Encryption:** Capturing passwords in cleartext even if the user typed "https://". |
| **LLMNR/NBT-NS Poisoning** | Name Resolution Spoofing. | Responds to failed Windows name queries (e.g., misspelled server names). | **Hash Stealing:** Tricking Windows into sending NTLMv2 hashes for offline cracking (Responder). |
| **TCP Hijacking** | SEQ/ACK Number Synchronization. | Injects a packet into a TCP stream by predicting the current sequence number. | **Unauthorized Command Execution:** Sending a "Delete All" command through an already authenticated SSH/Telnet session. |
| **ICMP Redirect Attack** | ICMP Type 5 Manipulation. | Tells a host that the attacker's machine is a "better gateway" to a specific IP. | **Surgical MitM:** Rerouting traffic for only one specific server to avoid detection. |
| **Smurf Attack** | ICMP Echo Request + Broadcast. | Pings a broadcast address with the target's spoofed source IP. | **Amplification DoS:** Using an entire network to "shout" at a target simultaneously. |
| **BGP Hijacking** | AS-PATH Prepending / Prefix Hijacking. | Announces a more specific route to a block of IP addresses. | **Global Redirection:** Stealing traffic for entire organizations or countries at the ISP level. |
| **Rogue DHCP Server** | DHCP Offer Race Condition. | Sets up a fake server to give clients the attacker's IP as the DNS/Gateway. | **Complete Network Control:** Automatically positioning as the MitM for every new user. |
| **UDP Amplification** | Source Spoofing + Large Response. | Sends a tiny request to an NTP/Memcached server that results in a massive response to the target. | **Volumetric DoS:** Turning 10Mbps of attack traffic into 500Gbps of crushing power. |
| **IPv6 RA Guard Bypass** | Fragmented Router Advertisements. | Sends IPv6 config packets that are fragmented to bypass security filters. | **IPv6 Takeover:** Hijacking the IPv6 stack of a network that only thinks it's using IPv4. |
| **CAM Table Poisoning** | ARP/MAC Mapping Inversion. | Constantly re-announces a target's MAC to confuse the switch. | **Packet Redirection:** Stealing specific packets intended for another host on the same switch. |
| **HULK (HTTP Flood)** | Obfuscated HTTP GET Requests. | Generates unique URLs to bypass web caches and hit the backend database. | **Resource Exhaustion:** Crashing the SQL database behind a web application. |
| **LOIC / HOIC** | Multi-threaded Volumetric Flooding. | Brute force packet generation at the application and transport layer. | **Network Saturation:** Filling the target's internet pipe until no traffic can pass. |
| **SNMP Bruteforcing** | Community String Guessing. | Uses dictionary attacks to find "public" or "private" SNMP strings. | **Information Disclosure:** Extracting router configs and network maps from SNMP MIBs. |
| **TCP Reset Attack** | RST Flag Injection. | Sends a forged RST packet to a target to kill an active connection. | **Communication Disruption:** Dropping a VPN or SSH connection to force a re-authentication (for sniffing). |
| **VLAN Double Tagging** | Nested 802.1Q Tags. | Wraps an Ethernet frame in two VLAN tags to bypass switch tag-stripping. | **Uni-directional Attack:** Sending packets to a target VLAN that is physically isolated from the attacker. |
| **DNS Tunneling** | TXT/CNAME Record Encapsulation. | Encodes non-DNS data (like a Shell) inside DNS queries. | **Exfiltration:** Stealing data through a firewall that blocks everything except DNS (Port 53). |
| **IP Source Routing** | Loose/Strict Source Routing Options. | Tells the network exactly which routers to use for a packet's path. | **Perimeter Bypass:** Forcing a packet to go "around" a firewall by specifying a different path. |
| **IGMP Snarfing** | Multicast Group Joining. | Joins a multicast stream intended for specific servers/users. | **Eavesdropping:** Listening to video streams or sensitive multicast data on a corporate network. |
| **Packet Replay** | Capturing and Re-injecting Frames. | Resends a valid authentication packet captured earlier. | **Bypassing Auth:** Gaining access without knowing the password by replaying the handshake. |
| **Mitm6** | IPv6 DNS Takeover via DHCPv6. | Responds to IPv6 DHCP requests on a local network to set the attacker as the DNS server. | **Windows Credential Theft:** Forcing Windows to authenticate to a fake WPAD server. |
| **Tsunami (Syn-Ack Flood)** | Reflected SYN-ACK response. | Spoofs the target's IP while sending SYNs to thousands of random servers. | **Indirect DoS:** Making the target look like the attacker while they get flooded by "replies." |
| **DDoS Reflection** | SSDP / Portmap / CharGen abuse. | Uses old, misconfigured protocols to reflect traffic onto a target. | **Unattributable DoS:** Attacking a target using the resources of thousands of innocent home routers. |

<br>

---

<div id="türkçe-teknik-mükemmellik"></div>

# 🇹🇷 TÜRKÇE: TEKNİK MÜKEMMELLİK

### 🧠 1. KAVRAMSAL TEMEL: GÜVEN PARADOKSU
ARPANET'in ilk günlerinde bağlantı bir lükstü ve kullanıcılar güvenilir akademisyenlerdi. Protokoller **verimlilik ve teslimat** için tasarlandı, **saldırgan direnci** için değil. Bu, **Güven Paradoksu**'nu yarattı: Bir ağın çalışabilmesi için düğümlerin (nodes), komşularının kimlik beyanlarına güvenmesi gerekir.

* **Doğuştan Gelen Güven Kusuru:** Katman 2'de (Veri Bağlantısı), bir MAC adresinin temsil ettiğini iddia ettiği IP'ye ait olduğuna güveniriz. Katman 3'te (Ağ), bir paketin kaynak IP'sinin meşru olduğuna güveniriz.
* **Hacker'ın Hedefi:** Ofansif güvenlik, hedef sistemi amaçlanmayan bir duruma zorlayan hatalı veya dürüst olmayan meta veriler enjekte ederek bu "kör güveni" sömürme sanatıdır.

### ⚙️ 2. SİSTEM MİMARİSİ: KERNEL VS. KABLO
Ağ saldırıları, öncelikle **İşletim Sistemi Çekirdeği (Kernel)** içindeki sistem kaynakları üzerinde verilen bir savaştır.

* **Netfilter ve Kancalar (Hooks):** Linux'ta her paket Netfilter çerçevesinden geçer. Bir saldırı (SYN Flood gibi), çekirdeği milyonlarca çöp paket için kuralları değerlendirmek üzere CPU döngüleri harcamaya zorlar.
* **Bağlam Değiştirme (Context Switching) & IRQ'lar:** Paketler aşırı hızda ulaştığında, CPU bir **Kesme Fırtınası (Interrupt Storm)** durumuna zorlanır. Donanım Kesmelerini (Kernel) işlemek için uygulama işlemlerini (Userland) durdurur, bu da sistemin tamamen çökmesine neden olur.
* **sk_buff Yapısı:** Her paket `sk_buff` adı verilen bir çekirdek bellek yapısını işgal eder. Çok sayıda küçük paket göndererek çekirdeğin **Slab Ayırıcı (Slab Allocator)** belleğini tüketebilir, bant genişliği düşük olsa bile sistemin kilitlenmesine neden olabiliriz.



### 💀 3. PROTOKOL VE DURUM MANTIĞI: İSTİSMAR EDİLEN EL SIKIŞMA
TCP/IP yığını **Belirlenimci bir Durum Makinesidir (Deterministic State Machine)**. Her durumun (SYN_RECV, ESTABLISHED, FIN_WAIT) belirli bir kural seti vardır.

* **Sıra Numarası Ele Geçirme (Sequence Number Hijacking):** TCP, veriyi takip etmek için 32-bitlik Sıra (SEQ) ve Onay (ACK) numaralarını kullanır. Bir saldırgan bu numaraları tahmin edebilirse (genellikle Kernel'ın PRNG'sindeki düşük entropi nedeniyle), mevcut bir oturuma "Komut" paketi enjekte edebilir.
* **Bekleme Kuyruğu (The Backlog Queue):** Bir sunucu SYN aldığında, bağlantıyı `SYN_RECV` durumuna sokar ve **Dinleme Bekleme Kuyruğu**'na (Listen Backlog) kaydeder. Son ACK'yı asla göndermeyerek bu kuyruğu doldurur, sunucunun yeni ve meşru bağlantıları kabul etmesini engelleriz.



### 4. ZAYIFLIĞIN DOĞUŞU: YAPISAL GÜVEN
Zayıflık, **Tasarım Tavizlerinin** bir yan ürünüdür:

1.  **ARP (Adres Çözümleme Protokolü):** Bir "Doğrulama" aşaması olmadan tasarlanmıştır. Kimse sormasa bile herkes ARP yanıtı gönderebilir. Bu, **Aradaki Adam (MitM)** pozisyonlarına izin verir.
2.  **BGP (Sınır Geçit Protokolü):** "İnternetin Tutkalı", "Google'ın IP'sine giden en iyi yol bende" diyen her yönlendiriciye (router) güvenir. Bu, tüm ülkelerin internet erişimini kaybedebileceği **BGP Hijacking** saldırılarına olanak tanır.
3.  **Kimlik Doğrulama Eksikliği:** DNS ve DHCP gibi protokoller imzalarla tasarlanmamıştır. İlk yanıt veren kişiye inanılır. Bu "Yarış Durumu" (Race Condition), çoğu yönlendirme saldırısının temelidir.



---

# 🛠️ YÜCE CEPHANELİK (İLERİ DÜZEY SALDIRI TABLOSU)
*En ölümcül ağ sömürü teknikleri için kapsamlı bir rehber.*

| Teknik / Araç | Atomik Mekanizma | Eğitsel Açıklama (Mantık) | 💀 Stratejik Saldırgan Hedefi |
| :--- | :--- | :--- | :--- |
| **ARP Sahteciliği** | Karşılıksız ARP (Opcode 2) enjeksiyonu. | Hangi MAC adresinin hangi IP'ye sahip olduğu konusunda yalan söyleyerek trafiği Katman 2'de yönlendirir. | **MitM:** Kimlik bilgilerini çalmak için açık metin trafiğini (HTTP/FTP/Telnet) izlemek. |
| **SYN Flood** | `tcp_max_syn_backlog` biriminin tüketilmesi. | Sunucunun "yarı-açık" bağlantı belleğini doldurur, böylece sunucu başka kimseyle konuşamaz. | **Hizmet Reddi (DoS):** Çok düşük bant genişliği ile yüksek erişilebilirlikli bir sunucuyu düşürmek. |
| **Slowloris** | Tamamlanmamış HTTP Başlıkları. | HTTP soketlerini sonsuz yavaşlıkta başlıklar göndererek açık tutar. | **Uygulama DoS:** Tüm işçi iş parçacıklarını kullanarak bir web sunucusunu (Apache/Nginx) kilitlemek. |
| **DNS Önbellek Zehirlemesi** | İşlem Kimliği (TXID) Kaba Kuvvet saldırısı. | Özyinelemeli bir çözücünün önbelleğine sahte bir DNS kaydı enjekte eder. | **Trafik Yönlendirme:** Alan adı çözünürlüğünü ele geçirerek kullanıcıları sahte oltalama sitelerine göndermek. |
| **DHCP Aç bırakma** | MAC Adresi Sahteciliği (DHCP Discover). | Havuzdaki her IP'yi talep ederek dağıtacak IP bırakmaz. | **Ağ Reddi:** Yeni ve meşru cihazların IP almasını ve ağa katılmasını engellemek. |
| **MAC Flooding** | CAM Tablosu Taşması. | Bir anahtarı (switch) rastgele MAC'lerle doldurarak arama tablosunu felç eder. | **Fail-Open:** Anahtarı bir hub gibi davranmaya zorlayarak tüm trafiği sniffing için her porta yaymasını sağlamak. |
| **VLAN Hopping** | DTP (Dynamic Trunking Protocol) İstismarı. | Kısıtlı VLAN'lara erişmek için bir anahtarla trunk hattı müzakere eder. | **Yetki Yükseltme:** "Misafir" ağından "Yönetim" veya "Sunucu" ağına zıplamak. |
| **STP Root Ele Geçirme** | BPDU (Bridge Protocol Data Unit) enjeksiyonu. | Ağın merkezi (Root Bridge) olmak için 0 önceliğe sahip bir BPDU gönderir. | **Trafik Düzenleme:** Analiz için tüm ağ trafiğini saldırganın anahtarı üzerinden geçmeye zorlamak. |
| **SSL Stripping** | Proxy tabanlı 302 Yönlendirmesi. | Tarayıcı şifrelemeye geçmeden önce bir HTTPS isteğini HTTP'ye düşürür. | **Şifreleme Baypası:** Kullanıcı "https://" yazsa bile parolaları açık metin olarak yakalamak. |
| **LLMNR/NBT-NS Zehirleme** | İsim Çözümleme Sahteciliği. | Başarısız Windows isim sorgularına yanıt verir (örn: yanlış yazılmış sunucu isimleri). | **Hash Hırsızlığı:** Windows'u çevrimdışı kırma amacıyla NTLMv2 hashleri göndermeye zorlamak. |
| **TCP Hijacking** | SEQ/ACK Numarası Senkronizasyonu. | Mevcut sıra numarasını tahmin ederek bir TCP akışına paket enjekte eder. | **Yetkisiz Komut Yürütme:** Halihazırda kimliği doğrulanmış bir oturum üzerinden komut göndermek. |
| **ICMP Redirect Saldırısı** | ICMP Tip 5 Manipülasyonu. | Hedefe, saldırganın makinesinin belirli bir IP için "daha iyi bir geçit" olduğunu söyler. | **Cerrahi MitM:** Tespit edilmemek için sadece belirli bir sunucu trafiğini yeniden yönlendirmek. |
| **Smurf Saldırısı** | ICMP Echo İsteği + Yayın (Broadcast). | Hedefin sahte kaynak IP'si ile bir yayın adresine ping atar. | **Yükseltme DoS:** Bir hedefi aynı anda boğmak için tüm ağın "bağırmasını" sağlamak. |
| **BGP Hijacking** | AS-PATH Ekleme / Prefix Ele Geçirme. | ISS seviyesinde bir IP bloğuna giden daha spesifik bir yol anons eder. | **Küresel Yönlendirme:** Kurumların veya ülkelerin trafiğini ISS seviyesinde çalmak. |
| **Sahte DHCP Sunucusu** | DHCP Yanıtı Yarış Durumu. | İstemcilere saldırganın IP'sini DNS/Gateway olarak veren sahte bir sunucu kurar. | **Tam Ağ Kontrolü:** Her yeni kullanıcı için otomatik olarak MitM konumuna yerleşmek. |
| **UDP Amplification** | Kaynak Sahteciliği + Büyük Yanıt. | Hedefe devasa bir yanıt dönmesi için NTP/Memcached sunucularına minik istekler atar. | **Volumetrik DoS:** 10Mbps'lik saldırı trafiğini 500Gbps'lik yıkıcı bir güce dönüştürmek. |
| **IPv6 RA Guard Baypası** | Parçalanmış Router Anonsları. | Güvenlik filtrelerini aşmak için parçalanmış IPv6 konfigürasyon paketleri gönderir. | **IPv6 Ele Geçirme:** Sadece IPv4 kullandığını sanan bir ağın IPv6 yığınını ele geçirmek. |
| **CAM Tablosu Zehirleme** | ARP/MAC Eşleme İnversiyonu. | Anahtarı (Switch) şaşırtmak için sürekli bir hedefin MAC'ini anons eder. | **Paket Yönlendirme:** Aynı anahtar üzerindeki başka bir host için tasarlanmış spesifik paketleri çalmak. |
| **HULK (HTTP Flood)** | Karmaşıklaştırılmış HTTP GET İstekleri. | Web önbelleklerini atlayıp veritabanına vurmak için benzersiz URL'ler üretir. | **Kaynak Tüketimi:** Web uygulamasının arkasındaki SQL veritabanını çökertmek. |
| **LOIC / HOIC** | Çok kanallı Volumetrik Sel. | Uygulama ve taşıma katmanında kaba kuvvet paket üretimi yapar. | **Ağ Doygunluğu:** Hedefin internet hattını hiçbir trafiğin geçemeyeceği kadar doldurmak. |
| **SNMP Bruteforcing** | Community String Tahmini. | "public" veya "private" SNMP dizelerini bulmak için sözlük saldırıları kullanır. | **Bilgi Sızıntısı:** SNMP MIB'lerinden yönlendirici konfigürasyonlarını ve ağ haritalarını çekmek. |
| **TCP Reset Saldırısı** | RST Bayrağı Enjeksiyonu. | Aktif bir bağlantıyı öldürmek için hedefe sahte bir RST paketi gönderir. | **İletişim Kesintisi:** Şifreleri koklamak için bir VPN veya SSH bağlantısını yeniden kimlik doğrulamaya zorlamak. |
| **VLAN Çift Etiketleme** | İç İçe 802.1Q Etiketleri. | Switch etiket temizleme işlemini atlamak için bir çerçeveyi iki VLAN etiketiyle sarar. | **Tek Yönlü Saldırı:** Saldırgandan fiziksel olarak izole edilmiş bir hedef VLAN'a paket göndermek. |
| **DNS Tunneling** | TXT Kaydı Kapsülleme. | DNS sorgularının içine DNS dışı verileri (Shell gibi) kodlar. | **Veri Sızdırma:** Port 53 hariç her şeyi engelleyen bir güvenlik duvarı üzerinden veri çalmak. |
| **IP Kaynak Yönlendirme** | Gevşek/Sıkı Kaynak Yönlendirme Seçenekleri. | Ağa, bir paketin yolu için tam olarak hangi yönlendiricilerin kullanılacağını söyler. | **Çevre Güvenliği Baypası:** Farklı bir yol belirterek paketi bir güvenlik duvarının "etrafından" dolaşmaya zorlamak. |
| **IGMP Snarfing** | Multicast Gruba Katılım. | Belirli sunucular/kullanıcılar için tasarlanmış bir multicast akışına katılır. | **Dinleme:** Kurumsal ağdaki video akışlarını veya hassas multicast verilerini izinsiz izlemek. |
| **Paket Yeniden Oynatma** | Çerçeveleri Yakalama ve Yeniden Enjekte Etme. | Daha önce yakalanmış geçerli bir kimlik doğrulama paketini tekrar gönderir. | **Kimlik Doğrulama Baypası:** Parolayı bilmeden el sıkışmayı yeniden oynatarak erişim sağlamak. |
| **Mitm6** | DHCPv6 üzerinden IPv6 DNS Ele Geçirme. | Yerel ağdaki IPv6 DHCP isteklerine yanıt vererek saldırganı DNS sunucusu olarak atar. | **Windows Kimlik Bilgisi Hırsızlığı:** Windows'u sahte bir WPAD sunucusuna kimlik doğrulamaya zorlamak. |
| **Tsunami (Syn-Ack Flood)** | Yansıtılmış SYN-ACK yanıtı. | Binlerce rastgele sunucuya SYN gönderirken hedefin IP'sini taklit eder. | **Dolaylı DoS:** Hedefin saldırgan gibi görünmesini sağlarken hedefe yanıt yağmasına neden olur. |
| **DDoS Reflection** | SSDP / Portmap / CharGen suistimali. | Trafiği bir hedefe yansıtmak için eski ve kötü yapılandırılmış protokolleri kullanır. | **İzlenemez DoS:** Binlerce masum ev yönlendiricisinin kaynaklarını kullanarak bir hedefe saldırmak. |

---

### 🏁 ÖĞRENME SONUCU
Bu tekniklerde ustalaşarak, ağın bir **kullanıcısından**, ağın bir **mimarına** dönüşürsünüz. 

* **Mantık Anahtardır:** Sadece `ettercap` çalıştırmayın; bir ARP paketinin `Opcode` alanını manipüle ettiğinizi anlayın.
* **Ağ Dürüsttür:** Biz, protokollerin kurallara uymak üzere tasarlandığı gerçeğini sömürüyoruz.
* **Görünürlük Savunmadır:** Bu saldırıları durdurmanın tek yolu onları görmektir. Bu yüzden **Protokol Analizi (Wireshark)** bu modülün ön koşuluydu.

> **"Kablodaki kuralları bilen, kabloyu kendi iradesine göre nasıl bükeceğini de bilir."**

---
**Durum:** `SUPREME_OFFENSIVE_REFERENCE_COMPLETE`  
**Revizyon:** 1.5 (Ocak 2026)  
**Yazar:** Siber Savaş Mimarı