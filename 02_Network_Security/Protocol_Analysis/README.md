<p align="center">
  <img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=250&section=header&text=PROTOCOL%20ANALYSIS%20%26%20TRAFFIC%20FORENSICS&fontSize=32&animation=fadeIn&fontColor=ffffff&gradient=00BFFF,8A2BE2" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/DOMAIN-TRAFFIC_ANALYSIS-blueviolet?style=for-the-badge&logo=wireshark" />
  <img src="https://img.shields.io/badge/LAYER-PACKET_LEVEL-blue?style=for-the-badge&logo=cisco" />
  <img src="https://img.shields.io/badge/LEVEL-DEEP_PACKET_INSPECTION-black?style=for-the-badge" />
  <img src="https://img.shields.io/badge/STANDARD-PCAP_FORENSICS-success?style=for-the-badge" />
</p>

---

## 🧭 NAVIGATION

| 🇺🇸 [ENGLISH: FULL TECHNICAL DOCUMENTATION](#-english-technical-supremacy) |
| :--- |
| 🇹🇷 [TÜRKÇE: TAM TEKNİK DOKÜMANTASYON](#-türkçe-teknik-mükemmellik) |

---

<div id="english-technical-supremacy"></div>

# 🇺🇸 ENGLISH: TECHNICAL SUPREMACY

### 1. CONCEPTUAL FOUNDATION
**Why Protocol Analysis?** Because logs can be deleted, files can be hidden, but **packets never lie**. Every digital action—from a login attempt to a file download—must travel across the wire as electrical signals or light. Protocol Analysis is the art of capturing these fleeting signals, freezing them in time (PCAP), and dissecting them to reveal the absolute truth of what happened.

* **The Problem:** Networks are fast and ephemeral. Once a packet arrives, it's gone.
* **The Solution:** **Promiscuous Mode**. We tell the network card (NIC) to stop ignoring traffic not meant for us and instead capture *everything*.
* **The Conflict:** Encryption (TLS/SSL). While we can capture everything, we cannot read everything unless we have the keys or attack the implementation.

### 2. SYSTEM ARCHITECTURE
How does a sniffer work at the Kernel level?

* **The NIC (Network Interface Card):** Normally, it drops packets not addressed to its MAC. In **Promiscuous Mode**, it passes every frame to the CPU.
* **Kernel Bypass (BPF):** Tools like Wireshark use **libpcap** (Linux) or **Npcap** (Windows). This library interacts with the Kernel to copy packets from the network stack buffer directly to the user-space application *before* the OS firewall processes them.
* **Berkeley Packet Filter (BPF):** A virtual machine inside the kernel. When you type `tcp port 80`, the kernel compiles this into bytecode to filter packets *before* copying them to user-space, saving massive CPU cycles.



### 3. THE TRUTH ON THE WIRE
Protocols are languages. To be a hacker, you must learn grammar.
* **Encapsulation:** Data is wrapped like a Russian doll. `[Ethernet [IP [TCP [HTTP [Data]]]]]`.
* **The Handshake:** Analysis begins by verifying the connection logic. Did the SYN receive an ACK? If not, is there a firewall dropping it?
* **The Payload:** The actual data. In cleartext protocols (Telnet, HTTP, FTP), this is where credentials live.



[Image of OSI Model Encapsulation]


### 4. WHERE WEAKNESS EMERGES
1.  **Cleartext by Design:** Many protocols (HTTP, FTP, SMTP, DNS) were designed in the 1980s without encryption. They broadcast passwords to anyone listening on the local network.
2.  **Protocol Anomalies:** Malware often communicates oddly. Beacons at exact 5-second intervals, using port 80 for non-HTTP traffic, or huge DNS queries (Tunneling) are dead giveaways.
3.  **ARP Spoofing:** By abusing the trust of Layer 2, an attacker can become the "Man-in-the-Middle," forcing all traffic to pass through their analyzer.



### 5. THE ARSENAL — TEACHING TABLE

| Tool / Technique | Low-Level Mechanism | Educational Explanation | Professional Insight |
| :--- | :--- | :--- | :--- |
| **Wireshark** | GUI for `libpcap` / Dissectors. | It captures packets and uses "Dissectors" to decode binary data into readable text (e.g., showing "GET /index.html" instead of hex bytes). | The gold standard for forensics. Used to reconstruct hacked sessions, find malware beacons, and debug network failures. |
| **Tcpdump** | CLI Packet Capture. | Direct interface to BPF. Minimal overhead compared to Wireshark. | Used on headless servers (SSH only) or for quick, surgical captures without crashing the system GUI. |
| **Tshark** | Command-line Wireshark. | Allows scripting packet analysis. Can pipe output to other tools. | Automating the detection of anomalies in massive PCAP files (e.g., "Find all credit card numbers in 10GB of traffic"). |
| **BPF Filters** | Kernel-level Bytecode. | Logic like `host 10.0.0.1 and port 80`. Filters packets *before* they consume memory. | Essential skill. Without BPF, your hard drive will fill up with useless noise (Netflix traffic) in seconds. |
| **Follow TCP Stream** | Session Reassembly. | Reorders packets by Sequence Number to show the full conversation as the user saw it. | Allows you to see the full email, HTML page, or chat log, rather than disjointed packets. |
| **Statistics > Endpoints** | Traffic Volume Analysis. | Aggregates packet counts by IP address. | Quickly spotting the "Top Talkers." If a printer is sending 5GB of data to Russia, you find it here. |
| **DNS Traffic Analysis** | UDP Port 53 Inspection. | Looking at DNS Queries (`A`, `TXT`, `MX`). | Malware often uses DNS to hide commands (DNS Tunneling). Long, random subdomains are a huge red flag. |
| **ARP Analysis** | Layer 2 Broadcasts. | Inspecting `Who has IP X?` frames. | Detecting ARP Spoofing. If one MAC address claims to be the Gateway router, you are under attack. |
| **HTTP/SMB Object Export** | File Reassembly. | Extracting binary files transferred over the network stream. | You can pull images, PDFs, or even executables directly out of the captured traffic for analysis. |
| **TLS Handshake Analysis** | Client/Server Hello inspection. | Looking at the `Client Hello` packet (SNI field) to see which website is being visited, even if encrypted. | Even in encrypted traffic, the domain name is often visible initially. Useful for identifying malicious domains. |



---
<div id="türkçe-teknik-mükemmellik"></div>

# 🇹🇷 TÜRKÇE: TEKNİK MÜKEMMELLİK

<hr style="height:4px;border-width:0;color:#8A2BE2;background-color:#8A2BE2">

### 1. KAVRAMSAL TEMELLER (MANTIK)
**Neden Protokol Analizi?** Çünkü loglar silinebilir, dosyalar gizlenebilir ama **paketler asla yalan söylemez**. Bir giriş denemesinden dosya indirmeye kadar her dijital eylem, kablo üzerinden elektrik veya ışık sinyali olarak geçmek zorundadır. Protokol Analizi, bu uçucu sinyalleri yakalama, zaman içinde dondurma (PCAP) ve ne olduğunu kesin bir doğrulukla ortaya çıkarma sanatıdır.

* **Sorun:** Ağlar hızlı ve geçicidir. Paket ulaştığı an yok olur.
* **Çözüm:** **Promiscuous (Karma) Mod**. Ağ kartına (NIC), sadece kendisine gelen paketleri almayı bırakıp, *her şeyi* yakalamasını emrederiz.
* **Çatışma:** Şifreleme (TLS/SSL). Her şeyi yakalayabilsek de, anahtarlara sahip değilsek veya uygulamaya saldırmazsak içeriği okuyamayız.

### 2. SİSTEM MİMARİSİ (NASIL ÇALIŞIR?)
Bir koklayıcı (sniffer) Kernel seviyesinde nasıl çalışır?

* **NIC (Ağ Kartı):** Normalde kendi MAC adresine gelmeyen paketleri çöpe atar. **Promiscuous Mod**'da ise her çerçeveyi (frame) CPU'ya iletir.
* **Kernel Bypass (BPF):** Wireshark gibi araçlar **libpcap** (Linux) veya **Npcap** (Windows) kullanır. Bu kütüphane, paketleri ağ yığını tamponundan (buffer) alıp, OS güvenlik duvarı daha işlemeden *önce* kullanıcı alanına kopyalar.
* **Berkeley Packet Filter (BPF):** Çekirdek içinde çalışan sanal bir makinedir. Siz `tcp port 80` yazdığınızda, çekirdek bunu bytecode'a derler ve paketleri kullanıcı alanına kopyalamadan *önce* filtreler, böylece muazzam CPU tasarrufu sağlar.

### 3. KABLODAKİ GERÇEK
Protokoller birer dildir. Hacker olmak için bu dilbilgisini (gramer) öğrenmelisiniz.
* **Kapsülleme (Encapsulation):** Veri, matruşka bebekleri gibi sarılır. `[Ethernet [IP [TCP [HTTP [Veri]]]]]`.
* **El Sıkışma:** Analiz, bağlantı mantığını doğrulamakla başlar. SYN paketi bir ACK aldı mı? Almadıysa, paketleri düşüren bir firewall mu var?
* **Yük (Payload):** Asıl veri. Açık metin protokollerde (Telnet, HTTP, FTP) kullanıcı adları ve şifreler burada yaşar.

### 4. ZAYIFLIĞIN DOĞUŞU
1.  **Tasarım Gereği Açık Metin:** Birçok protokol (HTTP, FTP, SMTP, DNS) 1980'lerde şifreleme olmadan tasarlandı. Yerel ağda dinleyen herkese parolaları yayınlarlar.
2.  **Protokol Anomalileri:** Zararlı yazılımlar (Malware) garip iletişim kurar. Tam 5 saniyede bir sinyal göndermek (Beacon), 80. portu HTTP olmayan trafik için kullanmak veya devasa DNS sorguları (Tünelleme) en büyük ipuçlarıdır.
3.  **ARP Zehirlemesi:** Layer 2'nin güvenini suistimal ederek, saldırgan kendini "Aradaki Adam" (MitM) konumuna sokar ve tüm trafiğin kendi analizcisinden geçmesini sağlar.

### 5. CEPHANELİK — EĞİTİM TABLOSU

| Araç / Teknik | Düşük Seviye Mekanizma | Eğitsel Açıklama | Profesyonel Görüş |
| :--- | :--- | :--- | :--- |
| **Wireshark** | `libpcap` ve Ayrıştırıcılar (Dissectors). | Paketleri yakalar ve ikili (binary) veriyi okunabilir metne çevirir (Örn: hex kodları yerine "GET /index.html" gösterir). | Adli bilişimde altın standarttır. Hacklenen oturumları yeniden oluşturmak ve malware izlerini bulmak için kullanılır. |
| **Tcpdump** | CLI Paket Yakalama. | BPF'ye doğrudan arabirimdir. Wireshark'a kıyasla sistem kaynağını çok az tüketir. | GUI olmayan sunucularda (SSH üzerinden) veya sistemi kilitlemeden hızlı, cerrahi kayıtlar almak için kullanılır. |
| **Tshark** | Komut Satırı Wireshark'ı. | Paket analizini scriptlemeye yarar. Çıktıyı başka araçlara aktarabilir. | Devasa PCAP dosyalarındaki anomalileri otomatize etmek için kullanılır (Örn: "10GB trafik içinde tüm kredi kartı numaralarını bul"). |
| **BPF Filtreleri** | Kernel-seviyesi Bytecode. | `host 10.0.0.1 and port 80` mantığı. Paketleri hafızaya almadan *önce* filtreler. | Hayati bir yetenektir. BPF olmazsa, sabit diskiniz saniyeler içinde gereksiz gürültüyle (Netflix trafiği vb.) dolar. |
| **TCP Akışını Takip Et** | Oturum Yeniden Birleştirme. | Paketleri Sıra Numarasına (SEQ) göre dizeyerek konuşmayı kullanıcının gördüğü gibi gösterir. | Parçalanmış paketler yerine e-postanın, HTML sayfasının veya sohbetin tamamını tek parça görmenizi sağlar. |
| **İstatistik > Uç Noktalar** | Trafik Hacmi Analizi. | Paket sayılarını IP adresine göre toplar. | "En Çok Konuşanları" (Top Talkers) hızla bulur. Eğer bir yazıcı Rusya'ya 5GB veri gönderiyorsa, buradan yakalarsınız. |
| **DNS Trafik Analizi** | UDP Port 53 İncelemesi. | DNS Sorgularına (`A`, `TXT`, `MX`) bakmak. | Malware genelde komutları gizlemek için DNS kullanır (DNS Tunneling). Uzun, rastgele alt alan adları büyük bir tehlike işaretidir. |
| **ARP Analizi** | Layer 2 Yayınları (Broadcast). | `IP X kimde?` çerçevelerini incelemek. | ARP Zehirlemesini tespit etmek. Eğer bir MAC adresi Gateway router olduğunu iddia ediyorsa saldırı altındasınızdır. |
| **HTTP/SMB Nesne Dışa Aktarma** | Dosya Birleştirme. | Ağ akışı üzerinden transfer edilen binary dosyaları ayıklamak. | Trafikten resimleri, PDF'leri ve hatta .exe dosyalarını analiz etmek üzere doğrudan çekebilirsiniz. |
| **TLS El Sıkışma Analizi** | Client/Server Hello incelemesi. | Trafik şifreli olsa bile `Client Hello` paketindeki SNI alanına bakarak hangi siteye gidildiğini görmek. | Şifreli trafikte bile alan adı (domain) başlangıçta görünür. Zararlı domainleri tespit etmek için çok yararlıdır. |

---

### 🏁 ÖĞRENME SONUCU

Bu modülü tamamladığınızda şunu anlamış olmalısınız: **Ağ, nihai tanıktır.**

* **Tahmin etmeyin;** paketi yakalayın.
* **Uygulamaya güvenmeyin;** kabloya güvenin.
* **Şifreleme içeriği gizler,** ama **davranışı** (zamanlama, boyut, hedef) gizleyemez.

> **"Loglar yöneticilere hizmet eder, paketler ise gerçeğe."**

---
**Status:** `SUPREME_PCAP_MASTER`
**Revision:** 2.0
**Author:** Network Forensics Architect