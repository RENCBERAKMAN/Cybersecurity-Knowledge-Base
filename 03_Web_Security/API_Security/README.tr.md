<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=API%20GÜVENLİK%20CEPHANELİĞİ&fontSize=40&animation=fadeIn&fontColor=ffffff&desc=Makineden%20Makineye%20Güvenin%20Kırılması&descFontSize=20&gradient=00FFFF,FF00FF" />

<br>

<img src="https://img.shields.io/badge/ALAN-API_HACKING-blueviolet?style=for-the-badge&logo=postman" />
<img src="https://img.shields.io/badge/STANDART-OWASP_API_TOP_10-green?style=for-the-badge&logo=owasp" />
<img src="https://img.shields.io/badge/PROTOKOL-REST_&_GRAPHQL-black?style=for-the-badge&logo=graphql" />
<img src="https://img.shields.io/badge/AUTH-JWT_&_OAUTH2-orange?style=for-the-badge&logo=json" />

</div>

<br>

> **"API, uygulamanın maskesiz halidir. Mantığı gizleyecek HTML, veriyi süsleyecek CSS yoktur. Veritabanına giden doğrudan bir boru hattıdır. Biz arayüzü hacklemeyiz; biz iş mantığının kendisini hackleriz."**

<br>

## 🧭 NAVİGASYON

| [1. Yaratılış: API'ler Neden Kanar?](#1-yaratılış-apiler-neden-kanar) | [2. Anatomi: REST, GraphQL ve JWT](#2-anatomi-rest-graphql-ve-jwt) | [3. OWASP API Top 10 Mantığı](#3-owasp-api-top-10-mantığı) | [4. Yüce Cephanelik (Saldırı Tablosu)](#-yüce-cephanelik) |
| :---: | :---: | :---: | :---: |

<br>

---

# 🇹🇷 TÜRKÇE: TEKNİK MÜKEMMELLİK

### 🧠 1. YARATILIŞ: API'LER NEDEN KANAR?
API Hacking'i anlamak için **Modern Mimariyi** anlamalısınız.
Eskiden sunucu HTML gönderirdi (Monolitik). Bugün sunucu **Ham Veri (JSON)** gönderiyor ve istemci (React, Mobil Uygulama) görünümü inşa ediyor.

* **İfşa:** İstemcinin arayüzü oluşturmak için *veriye* ihtiyacı olduğundan, API'ler genellikle **gerekenden fazla veri** (Kişisel veriler, kredi kartları) gönderir ve bunları gizleme işini ön yüz geliştiricisine bırakır. Biz hackerlar olarak ön yüzü görmezden gelir ve ham JSON yanıtına bakarız.
* **Güven Sorunu:** API'ler makineler için tasarlanmıştır. Geliştiriciler, "İstek benim Mobil Uygulamamdan geliyorsa geçerlidir" varsayımında bulunur. Unuttukları şey şudur: **İstekler uygulama dışında (Burp Suite veya Postman ile) tekrar edilebilir ve değiştirilebilir.**

### ⚙️ 2. ANATOMİ: REST, GRAPHQL VE JWT
Biz "sayfalara" saldırmıyoruz; **Uç Noktalara (Endpoints)** ve **Duruma (State)** saldırıyoruz.

#### A. REST (Representational State Transfer)
* **Yapı:** Kaynaklar üzerinde (`/users/105`) standart HTTP fiillerini (`GET`, `POST`, `PUT`, `DELETE`) kullanır.
* **Kusur:** ID'ye güvenmek. Eğer `PUT /users/105` isteğini `PUT /users/106` olarak değiştirirsem, API benim 106 numaralı kullanıcıya *sahip olup olmadığımı* kontrol ediyor mu? (Bu **BOLA**'dır).

#### B. GraphQL (Sorgu Dili)
* **Yapı:** Tek bir uç nokta (`/graphql`). İstemci tam olarak ne istediğini sorar.
* **Kusur:** **Introspection (İçe Bakış)**. Eğer açıksa, API'ye şunu sorabiliriz: *"Bana tüm veritabanı şemanı, tüm alanları ve gizli argümanları söyle."* Bu, banka müdüründen kasa dairesinin haritasını istemek gibidir.



#### C. JWT (JSON Web Tokens) - Durumsuz Anahtar
Oturumlar (sessions) yerine API'ler JWT kullanır (`Başlık.Yük.İmza`).
* **Atomik Mantık:** Sunucu veriyi gizli bir anahtarla imzalar.
* **Hack:**
    * **None Algoritması:** Sunucuya "Ben *hiçbir şeyle* imzalanmadım" deriz. Bazı kütüphaneler bunu kabul eder.
    * **Anahtar Karışıklığı (Key Confusion):** Sunucuyu, imza doğrulaması yaparken kendi *Açık Anahtarını (Public Key)* bir *HMAC Gizli Anahtarı (Secret)* gibi kullanmaya zorlarız.



---

### 💀 3. OWASP API TOP 10 MANTIĞI (2023 SÜRÜMÜ)

API sömürüsünün kutsal kitabı. Bunlar yazılım hatası (bug) değildir; mimari başarısızlıklardır.

#### API1: Broken Object Level Authorization (BOLA)
* **Kavram:** IDOR'un steroidli hali.
* **Saldırı:** `GET /receipts/500` -> `GET /receipts/501`.
* **Neden olur:** Kod "Kullanıcı giriş yapmış mı?" diye kontrol eder ama "Bu kullanıcı 501 numaralı makbuzu **sahipleniyor mu?**" diye kontrol etmeyi unutur.

#### API2: Broken Authentication (Kırık Kimlik Doğrulama)
* **Kavram:** Giriş kapısının zayıf korunması.
* **Saldırı:** Kimlik bilgisi doldurma (Credential Stuffing), zayıf JWT anahtarları veya `/login` üzerinde hız sınırı (rate limiting) olmaması.

#### API3: Broken Object Property Level Authorization (Mass Assignment / Toplu Atama)
* **Kavram:** Framework'ün istemci girdisini doğrudan dahili nesnelere bağlaması.
* **Saldırı:** Kullanıcı `{"username": "hacker", "role": "ADMIN"}` gönderir. Arayüzde "role" alanı olmasa bile, API bunu körü körüne kabul edip veritabanındaki yetkiyi güncelleyebilir.

#### API4: Unrestricted Resource Consumption (Sınırsız Kaynak Tüketimi)
* **Kavram:** Mantık yoluyla Hizmet Reddi (DoS).
* **Saldırı:** 1000 seviye derinliğinde iç içe geçmiş bir GraphQL sorgusu göndermek veya `page_size=1000000` istemek. Sunucu bu isteği karşılamaya çalışırken CPU/RAM tüketip çöker.

#### API5: Broken Function Level Authorization (BFLA)
* **Kavram:** Gizli yönetici butonlarını bulmak.
* **Saldırı:** Normal bir kullanıcı metodu `GET /users` yerine `DELETE /users` olarak değiştirir veya `/api/v1/admin/export_all` gibi bir URL'i tahmin eder.

---

# 🛠️ YÜCE CEPHANELİK
*API mantığını silahlandırmak için kesin rehber.*

| Teknik / Araç | Atomik Mekanizma | Eğitsel Mantık | 💀 Stratejik Saldırgan Hedefi |
| :--- | :--- | :--- | :--- |
| **BOLA / IDOR Avı** | ID Parametre Manipülasyonu. | API çağrılarındaki sayısal (`id=10`) veya UUID girdilerini değiştirerek başkalarının verisine erişmek. | **Veri Sızdırma:** Sayıları iterasyonla değiştirerek veritabanını kayıt kayıt dışarı aktarmak. |
| **Mass Assignment** | JSON Özellik Enjeksiyonu. | POST/PUT isteğine `"is_admin": true` veya `"balance": 99999` gibi alanlar enjekte etmek. | **Yetki Yükseltme:** Dahili değişken adını "tahmin ederek" yönetici olmak. |
| **JWT None Algo** | Başlık Manipülasyonu (`alg: None`). | İmza bölümünü silip sunucuya "Kriptoya gerek yok" demek. | **Kimlik Doğrulama Atlatma:** Gizli anahtarı bilmeden `{"sub": "admin"}` olarak giriş yapmak. |
| **GraphQL Introspection** | Şema Sorgulama (`__schema`). | API'den tüm dahili dökümantasyonunu ve tip tanımlarını döndürmesini istemek. | **Keşif (Recon):** Geliştiricilerin unuttuğu gizli "Gölge API'leri" veya savunmasız alanları keşfetmek. |
| **Webhooks ile SSRF** | Dış İstek Tetikleme. | Profil resmi URL'sini `http://169.254.169.254` (Bulut Metadata) olarak ayarlamak. | **Bulut Ele Geçirme:** API'yi kendi AWS/Azure kimlik bilgilerini çalmaya zorlamak. |
| **Race Conditions** | Paralel İstek Yürütme. | Veritabanı bakiyeyi güncellemeden önce aynı anda 10 tane "Kuponu Kullan" isteği göndermek. | **İş Mantığı Dolandırıcılığı:** Tek kullanımlık kuponu defalarca kullanmak veya olmayan parayı çekmek. |
| **Verb Tampering** | HTTP Metot Değiştirme. | ACL'leri atlatmak için `GET /users` isteğini `HEAD /users` veya `PUT /users` olarak değiştirmek. | **Erişim Kontrolü Atlatma:** GET üzerinde kısıtlı ama diğer metotlarda yanlışlıkla açık bırakılmış kaynaklara erişmek. |
| **Kiterunner** | Bağlam-Duyarlı Keşif. | Gerçek dünya API speklerinden (Swagger) türetilmiş kelime listeleriyle API rotalarını kaba kuvvetle taramak. | **Gölge API Keşfi:** Geliştiricilerin silmeyi unuttuğu `/api/v1/mobile/hidden_test` uç noktalarını bulmak. |
| **Parameter Pollution** | Çift Anahtar Enjeksiyonu. | `id=123&id=456` göndermek. WAF ilkine bakar, Uygulama ikincisini işler. | **WAF Atlatma:** Güvenlik filtrelerinin arkasından zararlı yükleri geçirmek. |
| **BOLA (GraphQL)** | İç İçe Nesne Gezintisi. | `{ user { posts { comments { author { email } } } } }` sorgusu atmak. | **Derin Veri Madenciliği:** İlişkisel nesneler üzerinden erişilmemesi gereken kişisel verileri (PII) çekmek. |

<br>

### 🛡️ SAVUNMA: DEĞİŞMEZ SÖZLEŞME
1.  **Şema Doğrulama:** OpenAPI spesifikasyonunda açıkça tanımlanmamış herhangi bir JSON alanını reddedin (Mass Assignment'ı durdurur).
2.  **Nesne Düzeyinde Kontroller:** Her veritabanı sorgusu `WHERE owner_id = current_user_id` içermelidir.
3.  **Hız Sınırlama (Rate Limiting):** Sadece IP'ye göre değil, Kullanıcı ID'sine ve spesifik Uç Noktaya göre sınırlayın (Brute Force/DoS'u durdurur).

> **"API, beyindir. Eğer beynin girdilerini kontrol ederseniz, makinenin gerçekliğini kontrol edersiniz."**

---
**Durum:** `SUPREME_API_REFERENCE`
**Revizyon:** 1.0 (Ocak 2026)
**Yazar:** Kıdemli Güvenlik Araştırmacısı