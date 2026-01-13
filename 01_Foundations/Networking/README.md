# 🛡️ ADVANCED NETWORKING: THE HACKER'S BLUEPRINT
> **"In the world of bits and bytes, the network is the battlefield. Master the packet, master the game."**

[🇺🇸 English Reference](#english-reference) | [🇹🇷 Türkçe Teknik Doküman](#türkçe-teknik-doküman)

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

## 🇺🇸 ENGLISH REFERENCE

### 🧬 OSI Model: The Attack Surface Mapping
A professional does not see layers; they see entry points.

| Layer | PDU | Protocol | Security Context & Attack Vectors |
| :--- | :--- | :--- | :--- |
| **7. Application** | Data | HTTP/S, DNS, SMB | SSRF, SQLi, API Exploitation, Buffer Overflows |
| **6. Presentation** | Data | SSL/TLS, SSH | Padding Oracle, Weak Cipher Suites, Heartbleed |
| **5. Session** | Data | RPC, NetBIOS | Session Hijacking, RPC Null Sessions, SMB Relay |
| **4. Transport** | Segment | TCP, UDP | SYN Flood, Port Scanning (Stealth), Session Splicing |
| **3. Network** | Packet | IP, ICMP, IPsec | IP Spoofing, ICMP Tunneling, BGP Hijacking, MITM |
| **2. Data Link** | Frame | ARP, 802.1Q | ARP Poisoning, VLAN Hopping, MAC Flooding |
| **1. Physical** | Bit | 802.11, Fiber | Rogue AP, Hardware Keyloggers, Signal Jamming |



▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

### ⚡ TCP Deep Dive: The Logic of Connection
Understanding TCP Flags is the difference between a Script Kiddie and a Security Engineer.

- **SYN (0x02):** "Let's talk." (Start of 3-way handshake)
- **ACK (0x10):** "I hear you." (Acknowledge packet)
- **RST (0x04):** "Connection error/Closed." (Forceful termination)
- **FIN (0x01):** "I'm done." (Graceful closure)
- **PSH (0x08):** "Process this immediately!" (No buffering)
- **URG (0x20):** "Critical data included."



> **💡 Pro Tip:** A `SYN` followed by an `RST/ACK` usually indicates a **Closed Port**, while no response often means the packet was **Dropped** by a Firewall.

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

## 🇹🇷 TÜRKÇE TEKNİK DOKÜMAN

### 🔍 Protokol Analizi ve Zafiyet İlişkilendirme
Siber güvenlik uzmanı için protokoller sadece veri taşımaz, sömürülecek mantık hataları barındırır.

#### 1. SMB (Server Message Block) - Port 445
* **Context:** Windows ağlarının bel kemiği.
* **Vulnerability:** SMB Signing devre dışıysa **SMB Relay** saldırıları mümkündür.
* **Critical Exploit:** EternalBlue (MS17-010).

#### 2. DNS (Domain Name System) - Port 53
* **Context:** İnternetin telefon rehberi.
* **Attack:** Zone Transfer (AXFR) ile tüm alt alan adlarının ifşası.
* **Stealth:** Veriyi DNS paketlerine gizleyerek Firewall üzerinden sızdırma (DNS Tunneling).

#### 3. SNMP (Simple Network Management Protocol) - Port 161/162
* **Context:** Cihaz yönetimi.
* **Leak:** Yanlış yapılandırılmış "Community Strings" (public/private) tüm ağ topolojisini sızdırabilir.

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

### 🛠️ COMMAND LINE ARSENAL (PROFESSIONAL TOOLKIT)

| Tool | Command | Advanced Usage Purpose |
| :--- | :--- | :--- |
| **Nmap** | `nmap -sS -Pn -vv --reason` | Stealth SYN Scan with packet analysis. |
| **Tcpdump** | `tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'` | Filter for incoming SYN packets only. |
| **SS** | `ss -tulpn` | Map PIDs to listening sockets with high performance. |
| **Dig** | `dig axfr @[DNS_IP] [Domain]` | Attempt an unauthorized DNS Zone Transfer. |
| **Hping3** | `hping3 -c 1 -S -p 80 [IP]` | Manual packet crafting for firewall rule testing. |

▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬▬

### 🧠 Master's Mindset
> "Bir ağın nasıl çalıştığını bilmiyorsan, onu nasıl bozacağını asla tam olarak öğrenemezsin. Her zaman `Wireshark` aç ve paketlerin canlı akışını izle. Teori yalan söyler, trafik asla."

---
*Last Updated: 2026-01-13*
*Status: Verified Reference*