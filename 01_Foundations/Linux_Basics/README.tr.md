<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=LINUX%20TEMELLERİ%20MASTERCLASS&fontSize=42&animation=fadeIn&fontColor=ffffff&desc=Sıfırdan%20İşletim%20Sistemi%20Hakimiyetine%20ve%20Hacker%20Vizyonuna&descFontSize=20&gradient=000000,00FF00" />

<br>

<img src="https://img.shields.io/badge/ALAN-SİSTEM_MİMARİSİ-black?style=for-the-badge&logo=linux" />
<img src="https://img.shields.io/badge/SEVİYE-TEMELDEN_UZMANLIĞA-red?style=for-the-badge&logo=hackthebox" />
<img src="https://img.shields.io/badge/ODAK-OFANSİF_TEMELLER-blue?style=for-the-badge&logo=bash" />
<img src="https://img.shields.io/badge/DİL-TÜRKÇE-success?style=for-the-badge&logo=turkey" />

</div>

<br>

> **"Linux bir işletim sistemi değildir; bir felsefedir. Acemiler komutları ezberler; ustalar ise sistemin nasıl nefes aldığını, süreçlerin bellekte nasıl dans ettiğini ve izinlerin nasıl büküleceğini anlar. Temelleri bilmeyen, zirveyi asla fethedemez."**

---

## 🧭 NAVİGASYON

| [1. Çekirdek Felsefe: Her Şey Bir Dosyadır](#1-çekirdek-felsefe-her-şey-bir-dosyadır) | [2. Dosya Sistemi Hiyerarşisi (FHS)](#2-dosya-sistemi-hiyerarşisi-taktiksel-harita) | [3. İzinler ve SUID Doktrini](#3-izinler-ve-suid-doktrini-dac-mimarisi) |
| :---: | :---: | :---: |
| [4. İşlem (Process) Anatomisi](#4-işlem-process-anatomisi-ve-yönetim) | [5. G/Ç Yönlendirme ve Boru Hatları](#5-gç-yönlendirme-ve-boru-hatları-pipes) | [6. Ortam (Environment) ve Kabuk (Shell)](#6-ortam-environment-ve-kabuk-mimarisi) |

---

<div id="1-çekirdek-felsefe-her-şey-bir-dosyadır"></div>

## 🧠 1. ÇEKİRDEK FELSEFE: HER ŞEY BİR DOSYADIR

Linux'u diğer sistemlerden (örneğin Windows'tan) ayıran en büyük özellik **"Everything is a file"** (Her şey bir dosyadır) mantığıdır. Bu bir metafor değil, teknik bir mimaridir.

### Atomik Analiz: VFS (Virtual File System)
Linux, donanımı ve yazılımı aynı arayüz üzerinden yönetmek için VFS (Sanal Dosya Sistemi) kullanır.
* **Metin Dosyası:** Bir dosyadır.
* **Sabit Disk (Örn: `/dev/sda`):** Bir dosyadır. Üzerine `write()` yaparsanız fiziksel diske yazarsınız.
* **Ağ Soketi:** Bir dosyadır. Karşı bilgisayara veri göndermek, ağ soketi dosyasına `write()` yapmaktan ibarettir.
* **İşlemler (Processes):** `/proc` dizini altındaki dosyalardır.

**Hacker Perspektifi:** Eğer bir sistemde her şey dosyaysa, sistemi ele geçirmek demek; **doğru dosyayı bulup, doğru okuma/yazma (r/w) izinlerine sahip olmak** demektir. Terminal sadece bu dosyalarla etkileşime giren bir metin arayüzüdür.

---

<div id="2-dosya-sistemi-hiyerarşisi-taktiksel-harita"></div>

## 🗺️ 2. DOSYA SİSTEMİ HİYERARŞİSİ: TAKTİKSEL HARİTA

Windows'taki `C:\` veya `D:\` mantığını unutun. Linux'ta tek bir kök (Root) vardır: `/`. Tüm diskler, USB bellekler ve ağ sürücüleri bu kökün altına monte edilir (Mount).

| Dizin | Atomik İşlevi | 💀 Saldırgan / Savunmacı Vizyonu |
| :--- | :--- | :--- |
| `/` | **Kök (Root):** Sistemdeki her şeyin başlangıç noktası. | Buraya sadece `root` kullanıcısı yazabilir. |
| `/bin` & `/sbin` | **Binaries:** Sistemin temel komutları (`ls`, `cat`, `ip`). `sbin` yönetici komutlarıdır. | Kötü amaçlı bir yazılım, buradaki komutları kendi zararlı versiyonlarıyla değiştirebilir (Binary Hijacking). |
| `/etc` | **Etcetera:** Tüm sistem yapılandırma (config) dosyaları buradadır. Binary içermez. | **Altın Madeni:** Parola hashleri (`/etc/shadow`), ağ ayarları, servis konfigürasyonları. İlk bakılacak yer. |
| `/home` & `/root` | **Kullanıcı Dizinleri:** Kişisel dosyalar, gizli yapılandırmalar (`.bashrc`). | Gizli `.ssh` klasöründeki özel anahtarları (`id_rsa`) ve `.bash_history` içindeki unutulmuş şifreleri çalmak. |
| `/tmp` | **Temporary:** Geçici dosyalar. Her yeniden başlatmada silinir. | **Sığınak:** Sistemdeki her kullanıcının buraya yazma izni vardır (`rwxrwxrwt`). Zararlı payload'lar ve Exploit'ler önce buraya indirilir. |
| `/var` | **Variable:** Sürekli değişen dosyalar. Loglar (`/var/log`), web sunucusu dosyaları (`/var/www`). | İzleri silmek için logların yok edildiği (`rm -rf /var/log/*`) veya dışa sızılan verilerin depolandığı yer. |
| `/dev` | **Devices:** Donanım aygıtları (`/dev/null`, `/dev/sda`). | Veri yok etmek için `/dev/zero` veya rastgele şifreleme anahtarı üretmek için `/dev/urandom` kullanımı. |
| `/proc` | **Process:** Aslında diskte yer kaplamayan, Kernel'in RAM'i dosya gibi gösterdiği sanal dizin. | Çalışan tüm uygulamaların bellek dökümlerini okumak, şifreleri RAM üzerinden (Memory Scraping) çekmek. |



---

<div id="3-izinler-ve-suid-doktrini-dac-mimarisi"></div>

## 🔐 3. İZİNLER VE SUID DOKTRİNİ (DAC MİMARİSİ)

Linux güvenliği, DAC (Discretionary Access Control) modeli üzerine kuruludur. Bir dosyanın 3 tür sahibi vardır: **Kullanıcı (u)**, **Grup (g)** ve **Diğerleri (o)**. Her birinin 3 hakkı vardır: **Read (r)**, **Write (w)**, **Execute (x)**.

### İzinlerin Matematiksel Mantığı
İzinler genellikle 8'lik (Octal) sayı sistemiyle ifade edilir:
* `4` = **Read (r)** (Okuma)
* `2` = **Write (w)** (Yazma / Değiştirme)
* `1` = **Execute (x)** (Çalıştırma / Dizine girme)

Örnek: `chmod 755 dosya.sh`
* `7 (4+2+1)`: Dosyanın sahibi okur, yazar, çalıştırır.
* `5 (4+1)`: Grup üyeleri okur ve çalıştırır (yazamaz).
* `5 (4+1)`: Diğer herkes okur ve çalıştırır.

### SUID (Set Owner User ID) Biti: Yetki Yükseltmenin Anahtarı
Normalde bir dosyayı çalıştırdığınızda, dosya **sizin** yetkilerinizle çalışır. Ancak SUID biti (`s` veya `4000`) ayarlanmışsa, dosya **dosyanın sahibinin** (genellikle root) yetkileriyle çalışır.

* **Neden var?** Sıradan bir kullanıcının kendi şifresini değiştirebilmesi için `/usr/bin/passwd` komutunu çalıştırması gerekir. Bu komut `/etc/shadow` (sadece root yazabilir) dosyasına yazar. Bu yüzden `passwd` komutunda SUID biti vardır; çalıştırıldığında anlık olarak `root` yetkisine çıkar.
* **Hacker Vizyonu:** Bir sistemde yetki yükseltmek (Root olmak) istiyorsanız, sistemde yanlışlıkla SUID biti verilmiş komutları ararsınız (`find / -perm -4000 2>/dev/null`). Eğer `nmap`, `vim`, `python` veya `bash` üzerinde bu bit varsa, bu araçların özelliklerini kullanarak anında root kabuğu (shell) elde edersiniz (GTFOBins metodolojisi).

---

<div id="4-işlem-process-anatomisi-ve-yönetim"></div>

## ⚙️ 4. İŞLEM (PROCESS) ANATOMİSİ VE YÖNETİM

Linux'ta her çalışan program bir işlemdir (Process) ve Kernel tarafından yönetilir.

### Yaşam Döngüsü: Fork() ve Execve()
Linux'ta yeni bir işlem sıfırdan yaratılmaz. Mevcut bir işlem kendini kopyalar (`fork`) ve sonra bu kopyanın içine yeni program yüklenir (`execve`).
* Her işlemin bir **PID**'si (Process ID) ve onu doğuran babasının bir **PPID**'si (Parent Process ID) vardır.
* Sistem açıldığında `PID 1` (Init/Systemd) başlar ve diğer her şey onun soyundan gelir.

### Sinyaller (Signals) ile Konuşmak
İşlemlerle konuşmak için klavye değil, Kernel sinyalleri kullanılır. `kill` komutu aslında bir programı öldürmez; ona sinyal gönderir.
* `SIGTERM (15)`: Uygulamaya "Lütfen kapan" der. Uygulama verilerini kaydedip kapanır (Nazik).
* `SIGKILL (9)`: Kernel uygulamayı anında hafızadan siler. Uygulamanın itiraz hakkı yoktur (Zorba).
* `SIGSTOP (19)`: Uygulamayı dondurur (Arka plana atar `Ctrl+Z`).

**Hacker Vizyonu:** Arka planda sessizce çalışan (`Daemon`) bir ters bağlantı (Reverse Shell) yakalandığında, PPID'si `1` olan (babası ölmüş, yetim kalmış) veya gizlenmiş işlemleri aramak, sızma belirtilerini (IoC) tespit etmenin en temel yoludur.

---

<div id="5-gç-yönlendirme-ve-boru-hatları-pipes"></div>

## 🔀 5. G/Ç YÖNLENDİRME VE BORU HATLARI (PIPES)

Terminalde yazılan komutlar sihirli bir şekilde ekrana gelmez. Linux her işlem için 3 adet Standart Veri Akışı (Data Stream) açar:

1.  **STDIN (0):** Standart Girdi (Genelde Klavye)
2.  **STDOUT (1):** Standart Çıktı (Ekrana yazdırılan başarılı sonuç)
3.  **STDERR (2):** Standart Hata (Ekrana yazdırılan hata mesajı)

### Yönlendirme (Redirection)
* `>` : Çıktıyı dosyaya yazar (Dosyayı sıfırlar). Örn: `echo "hack" > dosya.txt`
* `>>` : Çıktıyı dosyanın sonuna ekler (Sıfırlamaz).
* `2>/dev/null` : Hata mesajlarını (STDERR=2) Linux'un kara deliğine (`/dev/null`) gönderip ekranda kirlilik yaratmasını engeller. (Örn: Yetki hatası veren dosyaları gizlemek için).

### Boru Hatları (Pipes `|`)
Bir programın STDOUT çıktısını, diğer programın STDIN girdisi yapar. Linux'un Unix felsefesinin kalbidir: *"Her program sadece bir işi yapsın ama mükemmel yapsın."*

* **Saldırgan Örneği:** Sistemde bash kabuğu (shell) kullanabilen kullanıcıları bulmak:
  ```bash
  cat /etc/passwd | grep "/bin/bash" | awk -F: '{print $1}'