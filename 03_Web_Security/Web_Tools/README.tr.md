<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=WEB%20GÜVENLİK%20ARSENALİ&fontSize=42&animation=fadeIn&fontColor=ffffff&desc=Ofansif%20Araç%20Mimarisi%20ve%20Otomasyon%20Doktrini&descFontSize=20&gradient=FF0000,000000" />

<br>

<img src="https://img.shields.io/badge/KATEGORİ-SİLAHLANMA-red?style=for-the-badge&logo=kali-linux" />
<img src="https://img.shields.io/badge/SEVİYE-MASTER_TOOLKIT-black?style=for-the-badge&logo=rust" />
<img src="https://img.shields.io/badge/ODAK-OTOMASYON_&_FUZZING-blue?style=for-the-badge&logo=python" />
<img src="https://img.shields.io/badge/STANDART-SECLISTS-orange?style=for-the-badge&logo=github" />

</div>

<br>

> **"Araçlar, hackerın zihninin uzantısıdır; yerine geçecek şeyler değildir. Bir Script Kiddie araca güvenir; bir Uzman ise aracı yönetir. Eğer bir aracın arka planda hangi paketi gönderdiğini bilmiyorsan, o aracı kullanma."**

<br>

## 🧭 NAVİGASYON

| 1. [Yaratılış: Araç Felsefesi](#1-yaratılış-araç-felsefesi-ve-metodoloji) | 2. [Vekil Sunucular (Interceptors)](#2-vekil-sunucular-interceptors-zamanı-durdurmak) | 3. [Kaşifler (Enumerators)](#3-kaşifler-enumerators-görünmeyeni-haritalamak) | 4. [Yüce Cephanelik Tablosu](#4-yüce-cephanelik-araç-tablosu) |
| :---: | :---: | :---: | :---: |

<br>

---

# 🇹🇷 TÜRKÇE: TEKNİK MÜKEMMELLİK

### 🧠 1. YARATILIŞ: ARAÇ FELSEFESİ VE METODOLOJİ
Web güvenliğinde araçlar, **manuel çabanın ölçeklendirilmesi** için vardır. Bir insan günde 100 URL deneyebilir; bir araç saniyede 10.000 URL dener. Ancak, araçlar "mantık" kuramazlar.
* **Sıfır Noktası:** Hiçbir araç, bir iş mantığı hatasını (Business Logic Flaw) bulamaz. Örneğin, bir alışveriş sepetinde `-1` adet ürün ekleyip para kazanmayı sadece insan zekası bulur.
* **Hacker Vizyonu:** Biz araçları "Zafiyet Bulucu" olarak değil, **"Anomali Tespit Edici"** olarak kullanırız. Aracın görevi bize "Burada garip bir şey var" demektir; o garipliğin bir zafiyet olup olmadığına biz karar veririz.

### ⚙️ 2. VEKİL SUNUCULAR (INTERCEPTORS): ZAMANI DURDURMAK
Bu kategorideki araçlar, tarayıcı ile sunucu arasına girer. Trafiği durdurur, inceler ve manipüle eder. Bu, web güvenliğinin kalbidir.

#### A. Burp Suite Professional / Community
* **Atomik İşlev:** HTTP Proxy ve Man-in-the-Middle (MitM).
* **Derin Teknik:** Tarayıcının SSL sertifika zincirini (Chain of Trust) kendi ürettiği CA sertifikası ile kırarak şifreli trafiği (HTTPS) açık metin olarak okur ve düzenler.
* **Kullanım Amacı:** Manuel testler, parametre manipülasyonu, mantık hataları.

#### B. OWASP ZAP (Zed Attack Proxy)
* **Atomik İşlev:** Açık kaynaklı DAST (Dynamic Application Security Testing) aracı.
* **Derin Teknik:** "HUD" (Heads Up Display) teknolojisi ile tarayıcı ekranına enjekte ettiği JavaScript sayesinde, saldırı kontrollerini direkt web sayfası üzerinde gösterir.
* **Kullanım Amacı:** Otomatize taramalar ve CI/CD süreçlerine entegrasyon (Pipeline Security).


### 🗺️ 3. KAŞİFLER (ENUMERATORS): GÖRÜNMEYENİ HARİTALAMAK
Bir web sitesi, tarayıcıda gördüğünüzden ibaret değildir. Geliştiricilerin unuttuğu dizinler, yedek dosyalar ve alt alan adları (subdomains) buzdağının görünmeyen kısmıdır.

#### A. Fuzzing Mantığı (Ffuf / Gobuster)
* **Mekanizma:** Bir kelime listesindeki (Wordlist) her satırı alır, URL'in sonuna ekler (`site.com/ADMIN`, `site.com/LOGIN`) ve sunucudan dönen **HTTP Durum Koduna** (200, 403, 301) bakar.
* **Thread & Socket:** Bu araçlar Go veya Rust ile yazılmıştır. İşletim sisteminin "Socket" limitlerini zorlayarak saniyede binlerce istek açarlar.
* **Filtreleme:** `404 Not Found` dönen binlerce cevabı gizleyip, sadece `200 OK` dönenleri bize gösterirler.

#### B. Alt Alan Adı (Subdomain) Avcılığı
* **Pasif Keşif (Amass / Subfinder):** Hedef sunucuya hiç paket göndermeden, VirusTotal, Censys, Shodan gibi devasa veritabanlarını tarayarak `dev.site.com` gibi alt alan adlarını bulur.
* **Aktif Keşif (Puredns):** Bulunan alan adlarının gerçekten yaşayıp yaşamadığını DNS sorgularıyla doğrular.

### 🔫 4. OTOMASYON VE SÖMÜRÜ: AĞIR SİLAHLAR
Zafiyet bulunduğunda veya büyük ölçekli tarama gerektiğinde kullanılan "Topçu Birlikleri".

#### A. SQLMap
* **Atomik İşlev:** SQL Enjeksiyonu tespit ve sömürü motoru.
* **Teknik Derinlik:** Veritabanının türünü (MySQL, PostgreSQL, Oracle) parmak izinden tanır. "Boolean-Based Blind" tekniği ile veritabanına Evet/Hayır soruları sorarak veriyi bit-bit dışarı sızdırır.
* **Uzmanlık:** Sadece veritabanını çekmekle kalmaz; `os-shell` komutu ile veritabanı üzerinden işletim sisteminde komut çalıştırabilir.

#### B. Nuclei
* **Atomik İşlev:** Şablon tabanlı (Template-based) zafiyet tarayıcısı.
* **Teknik Derinlik:** YAML dosyaları ile yazılmış binlerce zafiyet imzasını (CVE'ler, yanlış yapılandırmalar) hedefe fırlatır. Gelen cevapta belirli bir string (örn: "root:x:0:0") varsa alarm verir.
* **Avantajı:** İnanılmaz hızlıdır ve topluluk tarafından sürekli güncellenir. Yeni bir zafiyet çıktığında (örn: Log4j), dakikalar içinde Nuclei şablonu yayınlanır.

---

# 🛠️ YÜCE CEPHANELİK (ARAÇ TABLOSU)

*Her profesyonelin `PATH` değişkeninde olması gereken araçlar.*

| Araç Adı | Kategori | Atomik Mekanizma (Nasıl Çalışır?) | 💀 Stratejik Kullanım Amacı |
| :--- | :--- | :--- | :--- |
| **Burp Suite** | Proxy | Yerel portta (8080) dinler, HTTP/S paketlerini yakalar. | **Cerrahi Müdahale:** Tek bir isteği değiştirip sunucunun mantığını bozmak (IDOR, Logic Flaws). |
| **Ffuf** | Fuzzer | Go dilinin "Goroutines" yapısını kullanarak yüksek hızlı HTTP istekleri atar. | **Dizin Keşfi:** Gizli `/admin`, `.git`, `.env` dosyalarını bulmak için sunucuyu kelime listesiyle bombalamak. |
| **SQLMap** | Exploitation | SQL sorgularına mantıksal hatalar enjekte eder ve dönen tepkiyi analiz eder. | **Veri Hırsızlığı:** Veritabanını komple dump etmek veya DB üzerinden sisteme sızmak (Shell). |
| **Nuclei** | Scanner | YAML şablonlarındaki kuralları (Regex, Matchers) HTTP yanıtlarında arar. | **Hızlı Tarama:** Binlerce alt alan adında bilinen CVE'leri (Log4j, Jira bugs) saniyeler içinde taramak. |
| **Amass** | Recon | OSINT kaynaklarını (API'ler, Sertifika Şeffaflığı logları) kazır. | **Varlık Keşfi:** Şirketin unuttuğu, internete açık tüm alt alan adlarını (Attack Surface) haritalamak. |
| **Curl** | Client | Ham HTTP istekleri oluşturur (`libcurl`). | **Manuel Doğrulama:** Bulunan bir zafiyeti terminalden hızlıca doğrulamak veya API ile konuşmak. |
| **Ncat (Netcat)** | Network | TCP/UDP soketlerini okur ve yazar. | **Reverse Shell:** Hedef sunucuda komut çalıştırıldığında bağlantıyı yakalamak (`nc -lvnp 4444`). |
| **John the Ripper** | Cracking | Hash algoritmalarını (MD5, SHA) tersine çevirmek için CPU/GPU kullanır. | **Parola Kırma:** Sızdırılan veritabanı hashlerini kırarak açık metin parolalara ulaşmak. |
| **Nikto** | Scanner | Web sunucusu parmak izlerini ve bilinen eski dosyaları tarar (Perl tabanlı). | **Eski Teknoloji Avı:** Unutulmuş CGI scriptlerini veya güncellenmemiş sunucu yazılımlarını bulmak. |
| **Wappalyzer** | Fingerprint | HTML başlıkları, JavaScript değişkenleri ve çerezlerden teknoloji tespiti yapar. | **Teknoloji Analizi:** Hedefin PHP mi, Node.js mi, WordPress mi kullandığını anlayıp saldırıyı ona göre şekillendirmek. |
| **Metasploit** | Framework | Hazır exploit modüllerini barındıran devasa bir çatıdır. | **Son Vuruş:** Doğrulanmış bir zafiyeti (örn: RCE) kullanarak tam sistem hakimiyeti (Meterpreter) sağlamak. |


### 🛡️ SAVUNMA VE TESPİT (BLUE TEAM NOTU)
* **Gürültü:** Bu araçların hepsi (özellikle Ffuf ve SQLMap) ağda inanılmaz gürültü çıkarır. WAF (Web Application Firewall) ve IPS sistemleri bunları imzalarından (User-Agent) hemen tanır.
* **Sessizlik:** Profesyonel kullanımda, araçların hızı düşürülmeli (Rate Limiting) ve User-Agent başlıkları değiştirilmelidir.

> **"En iyi araç senin beynindir. Diğer her şey sadece birer betiktir."**

---
**Durum:** `MASTER_TOOLKIT_READY`
**Revizyon:** 1.0 (Ocak 2026)
**Yazar:** Ofansif Güvenlik Mühendisi