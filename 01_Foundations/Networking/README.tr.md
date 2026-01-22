<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=260&section=header&text=AĞ%20TEMELLERİ%20VE%20MİMARİSİ&fontSize=35&animation=fadeIn&fontColor=ffffff&desc=Siber%20Güvenliğin%20Sinir%20Sistemi&descFontSize=20&gradient=000000,00FF00" />

<br>

<img src="https://img.shields.io/badge/ALAN-AĞ_MÜHENDİSLİĞİ-blue?style=for-the-badge&logo=cisco" />
<img src="https://img.shields.io/badge/SEVİYE-TEMEL_&_İLERİ-orange?style=for-the-badge&logo=wireshark" />
<img src="https://img.shields.io/badge/DİL-TÜRKÇE-red?style=for-the-badge&logo=turkey" />
<img src="https://img.shields.io/badge/ODAK-HACKER_PERSPEKTİFİ-black?style=for-the-badge&logo=kali-linux" />

</div>

---

# 🌐 AĞ GÜVENLİĞİNE GİRİŞ: BİTLERİN SAVAŞ ALANI

> **"Bir hacker için ağ, sadece bilgisayarları birbirine bağlayan kablolar değildir; manipüle edilebilir protokoller, atlatılabilir kurallar ve sömürülebilir güven ilişkileri bütünüdür."**

Bu doküman, klasik bir ağ dersi notu değildir. Buradaki her başlık, bir saldırganın (Red Team) o protokole nasıl baktığını ve bir savunmacının (Blue Team) neyi koruması gerektiğini anlatır.

---

## 📚 İÇİNDEKİLER

1.  [OSI Modeli: Saldırı Yüzeyini Anlamak](#1-osi-modeli-saldırı-yüzeyini-anlamak)
2.  [TCP/IP Protokol Kümesi: İnternetin Dili](#2-tcpip-protokol-kümesi-internetin-dili)
3.  [TCP vs UDP: Bağlantı Mantığı](#3-tcp-vs-udp-bağlantı-mantığı)
4.  [Önemli Protokoller ve Saldırı Vektörleri](#4-önemli-protokoller-ve-saldırı-vektörleri)
5.  [IP Adresleme ve Alt Ağlar (Subnetting)](#5-ip-adresleme-ve-alt-ağlar-subnetting)
6.  [Ağ Cihazları ve Güvenlik Rolleri](#6-ağ-cihazları-ve-güvenlik-rolleri)

---

## 1. OSI MODELİ: SALDIRI YÜZEYİNİ ANLAMAK

OSI (Open Systems Interconnection) modeli, ağ iletişimini 7 katmana böler. Bir güvenlik uzmanı için her katman, farklı bir saldırı türünü temsil eder.

| Katman | Adı | PDU (Veri Birimi) | Görevi | 💀 Hacker Perspektifi & Saldırı Türleri |
| :--- | :--- | :--- | :--- | :--- |
| **7** | **Uygulama** (Application) | Data | Kullanıcı ile etkileşim (HTTP, FTP, SMTP). | **Web Saldırıları:** SQL Injection, XSS, RCE, Phishing. Uygulama mantığındaki hatalar burada sömürülür. |
| **6** | **Sunum** (Presentation) | Data | Veri formatı ve şifreleme (SSL/TLS, JPEG). | **Şifreleme Hataları:** SSL Stripping, Padding Oracle, Malformed dosya upload saldırıları. |
| **5** | **Oturum** (Session) | Data | Bağlantıların yönetimi (NetBIOS, RPC). | **Oturum Çalma:** Session Hijacking, Token Replay saldırıları. |
| **4** | **Taşıma** (Transport) | Segment | Veri iletimi ve güvenilirliği (TCP, UDP). | **Tarama & DoS:** Port taramaları (Nmap), SYN Flood, UDP Amplification saldırıları. |
| **3** | **Ağ** (Network) | Paket | Rotalama ve Adresleme (IP, ICMP). | **Yönlendirme Saldırıları:** IP Spoofing (Kimlik Sahteciliği), Man-in-the-Middle (MITM), ICMP Tünelleme. |
| **2** | **Veri Bağlantı** (Data Link) | Çerçeve (Frame) | Fiziksel adresleme (MAC, ARP, VLAN). | **Yerel Ağ Saldırıları:** ARP Poisoning (Zehirleme), MAC Flooding, VLAN Hopping. |
| **1** | **Fiziksel** (Physical) | Bit | Kablolar, Sinyaller (Wi-Fi, Fiber). | **Donanımsal Müdahale:** Kablo dinleme (Wiretapping), Jamming (Sinyal bozma), Evil Twin (Sahte Wi-Fi). |

---

## 2. TCP/IP PROTOKOL KÜMESİ: İNTERNETİN DİLİ

OSI teoriktir, TCP/IP ise pratiktir. İnternet bu protokol yığını üzerinde çalışır.

### 🔥 3'lü El Sıkışma (Three-Way Handshake)
TCP güvenilirdir çünkü konuşmaya başlamadan önce "el sıkışır". Nmap gibi tarayıcılar bu el sıkışmayı manipüle ederek keşif yapar.

1.  **SYN (Synchronize):** İstemci sunucuya "Konuşabilir miyiz?" der. (Port açık mı?)
2.  **SYN-ACK (Synchronize-Acknowledge):** Sunucu açıksa "Evet, konuşabiliriz" der.
3.  **ACK (Acknowledge):** İstemci "Tamam, başlıyorum" der ve bağlantı kurulur.

> **💡 Kritik Bilgi:** Bir saldırgan `SYN` gönderip `SYN-ACK` aldığında, `ACK` göndermek yerine `RST` (Reset) gönderirse, sunucuda log bırakmadan portun açık olduğunu anlar. Buna **SYN (Stealth) Scan** denir.

### 🚩 TCP Bayrakları (Flags) ve Anlamları
Paketlerin niyetini bayraklar belirler.
* **SYN:** Bağlantı başlatma isteği.
* **ACK:** Onaylama.
* **RST:** Bağlantıyı aniden koparma (Hata veya reddetme).
* **FIN:** Bağlantıyı nazikçe sonlandırma.
* **PSH:** Veriyi tamponlamadan (buffer) hemen işle.
* **URG:** Öncelikli veri.

---

## 3. TCP vs UDP: BAĞLANTI MANTIĞI

| Özellik | TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
| :--- | :--- | :--- |
| **Bağlantı Türü** | Bağlantı tabanlı (Connection-oriented). | Bağlantısız (Connectionless). |
| **Güvenilirlik** | Veri ulaştı mı kontrol eder (ACK bekler). | Kontrol etmez, "Ateşle ve Unut" (Fire & Forget). |
| **Hız** | Yavaştır (El sıkışma ve kontrol mekanizmaları). | Çok hızlıdır. |
| **Kullanım Alanı** | Web (HTTP), Email (SMTP), Dosya (FTP). | Video yayını, Oyunlar, DNS, VoIP. |
| **Güvenlik Riski** | Durum (State) takibi kaynak tüketir (SYN Flood). | Kaynak adresi kolayca taklit edilebilir (Spoofing & DDoS Amplification). |

---

## 4. ÖNEMLİ PROTOKOLLER VE SALDIRI VEKTÖRLERİ

Bir siber güvenlikçi bu protokolleri ve zafiyetlerini ezbere bilmelidir.

### 🔹 ARP (Address Resolution Protocol) - Katman 2
* **Görevi:** IP adresini MAC adresine çevirir. "192.168.1.5 kimde?" diye bağırır (Broadcast).
* **Zafiyet:** ARP'de doğrulama yoktur. Herkes "O IP bende!" diyebilir.
* **Saldırı:** **ARP Poisoning (Zehirleme).** Saldırgan kendini modem gibi tanıtarak tüm ağ trafiğini kendi üzerinden geçirir (MITM).

### 🔹 DNS (Domain Name System) - Port 53 (UDP/TCP)
* **Görevi:** `google.com` ismini IP adresine çevirir.
* **Zafiyet:** Sorgular şifresizdir.
* **Saldırı:**
    * **DNS Spoofing:** Kullanıcıyı sahte banka sitesine yönlendirme.
    * **DNS Tunneling:** Güvenlik duvarını atlatmak için veriyi DNS sorguları içine gizleme.
    * **Zone Transfer:** Yanlış yapılandırılmış sunucudan tüm alt alan adlarını (subdomain) çekme.

### 🔹 DHCP (Dynamic Host Configuration Protocol) - Port 67/68 (UDP)
* **Görevi:** Ağdaki cihazlara otomatik IP dağıtır.
* **Saldırı:** **DHCP Starvation.** Saldırgan, sahte MAC adresleriyle havuzdaki tüm IP'leri tüketir. Sonrasında sahte bir DHCP sunucusu kurarak (Rogue DHCP) ağa girenleri kendine yönlendirir.

### 🔹 SMB (Server Message Block) - Port 445 (TCP)
* **Görevi:** Windows dosya ve yazıcı paylaşımı.
* **Kritik:** Windows ağlarında yatay hareket (Lateral Movement) için en çok kullanılan protokoldür.
* **Exploit:** EternalBlue (WannaCry fidye yazılımının kullandığı açık).

---

## 5. IP ADRESLEME VE ALT AĞLAR (SUBNETTING)

Ağı bölmek ve yönetmek için kullanılır.

* **IPv4:** 32-bit adres (Örn: 192.168.1.1).
* **IPv6:** 128-bit adres (Geleceğin standardı, daha güvenli ama karmaşık).
* **MAC Adresi:** Donanımın fiziksel adresi (Değiştirilebilir - MAC Spoofing).

### CIDR (Classless Inter-Domain Routing)
Hackerlar hedef kapsamını belirlemek için CIDR notasyonunu kullanır.
* `/24` -> 256 IP Adresi (Yerel Ağ taramaları için standart).
* `/16` -> 65,536 IP Adresi (Büyük kurum iç ağları).
* `/32` -> Tek bir IP (Belirli bir sunucuya saldırı).

---

## 6. AĞ CİHAZLARI VE GÜVENLİK ROLLERİ

### 🛑 Firewall (Güvenlik Duvarı)
* **Ne yapar?** Paketleri kurallara göre (IP, Port, Protokol) engeller veya izin verir.
* **Türleri:**
    * *Stateless:* Sadece pakete bakar.
    * *Stateful:* Bağlantının durumunu takip eder (Bu paket mevcut bir oturuma mı ait?).
    * *WAF (Web Application Firewall):* HTTP içeriğine bakar (SQLi, XSS engeller).

### 🔀 Switch vs Hub
* **Hub:** Aptaldır. Gelen veriyi herkese gönderir (Broadcast). Hub olan ağda `Wireshark` açarsan herkesin şifresini görürsün.
* **Switch:** Akıllıdır. Veriyi sadece hedef MAC adresine gönderir. Switch olan ağda başkasını dinlemek için **ARP Poisoning** yapman gerekir.

### 🌐 Router (Yönlendirici)
* **Ne yapar?** Farklı ağları (Örn: Ev ağı ile İnternet) birbirine bağlar.
* **NAT (Network Address Translation):** Evdeki 10 cihazı tek bir dış IP üzerinden internete çıkarır. Saldırgan dışarıdan içeriye doğrudan erişemez (Port Forwarding gerekir).

---

## 🛠️ PROFESYONEL AĞ ARAÇ ÇANTASI

Bir ağ uzmanının elinin altında her zaman bulunması gerekenler:

1.  **Wireshark:** Ağ trafiğini dinlemek ve analiz etmek (Paket Analizi).
2.  **Nmap:** Ağ haritalama, port tarama ve zafiyet tespiti.
3.  **Netcat (nc):** Ağın İsviçre çakısı. Port dinleme, veri transferi, reverse shell.
4.  **Tcpdump:** Komut satırı paket yakalayıcısı (GUI olmayan sunucular için).
5.  **Burp Suite:** Web trafiğini (HTTP) yakalamak ve manipüle etmek (Proxy).

---

### 🧠 SON SÖZ: ZİHNİYET
> "Ağ güvenliği, duvarlar örmek değil; kapıların nasıl çalıştığını, pencerelerin kilitlerini ve havalandırma boşluklarının nereye çıktığını bilmektir. Bir paketin yaşam döngüsünü anlamadan, onu koruyamazsın."

---
*Son Güncelleme: 2026-01-22*
*Durum: Tamamlandı - Referans Belgesi*