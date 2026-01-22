<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=260&section=header&text=NETWORK%20FUNDAMENTALS%20%26%20ARCHITECTURE&fontSize=35&animation=fadeIn&fontColor=ffffff&desc=The%20Nervous%20System%20of%20Cybersecurity&descFontSize=20&gradient=000000,00FF00" />

<br>

<img src="https://img.shields.io/badge/DOMAIN-NETWORK_ENGINEERING-blue?style=for-the-badge&logo=cisco" />
<img src="https://img.shields.io/badge/LEVEL-FOUNDATION_&_ADVANCED-orange?style=for-the-badge&logo=wireshark" />
<img src="https://img.shields.io/badge/LANG-ENGLISH-red?style=for-the-badge&logo=us" />
<img src="https://img.shields.io/badge/FOCUS-HACKER_PERSPECTIVE-black?style=for-the-badge&logo=kali-linux" />

</div>

---

# 🌐 INTRODUCTION TO NETWORK SECURITY: THE BATTLEFIELD OF BITS

> **"For a hacker, the network is not merely cables connecting computers; it is a collection of manipulatable protocols, bypassable rules, and exploitable trust relationships."**

This document is not a standard networking lecture note. Every topic here describes how an Attacker (Red Team) views a protocol and what a Defender (Blue Team) must protect.

---

## 📚 TABLE OF CONTENTS

1.  [The OSI Model: Understanding the Attack Surface](#1-the-osi-model-understanding-the-attack-surface)
2.  [The TCP/IP Protocol Suite: The Language of the Internet](#2-the-tcpip-protocol-suite-the-language-of-the-internet)
3.  [TCP vs UDP: Connection Logic](#3-tcp-vs-udp-connection-logic)
4.  [Critical Protocols and Attack Vectors](#4-critical-protocols-and-attack-vectors)
5.  [IP Addressing and Subnetting](#5-ip-addressing-and-subnetting)
6.  [Network Devices and Security Roles](#6-network-devices-and-security-roles)

---

## 1. THE OSI MODEL: UNDERSTANDING THE ATTACK SURFACE

The OSI (Open Systems Interconnection) model divides network communication into 7 layers. For a security professional, each layer represents a different type of attack.



| Layer | Name | PDU (Protocol Data Unit) | Function | 💀 Hacker Perspective & Attack Types |
| :--- | :--- | :--- | :--- | :--- |
| **7** | **Application** | Data | User interaction (HTTP, FTP, SMTP). | **Web Attacks:** SQL Injection, XSS, RCE, Phishing. Logic flaws in applications are exploited here. |
| **6** | **Presentation** | Data | Data formatting and encryption (SSL/TLS, JPEG). | **Encryption Flaws:** SSL Stripping, Padding Oracle, Malformed file upload attacks. |
| **5** | **Session** | Data | Session management (NetBIOS, RPC). | **Session Theft:** Session Hijacking, Token Replay attacks. |
| **4** | **Transport** | Segment | Data transmission and reliability (TCP, UDP). | **Scanning & DoS:** Port scanning (Nmap), SYN Floods, UDP Amplification attacks. |
| **3** | **Network** | Packet | Routing and Addressing (IP, ICMP). | **Routing Attacks:** IP Spoofing, Man-in-the-Middle (MITM), ICMP Tunneling. |
| **2** | **Data Link** | Frame | Physical addressing (MAC, ARP, VLAN). | **Local Network Attacks:** ARP Poisoning, MAC Flooding, VLAN Hopping. |
| **1** | **Physical** | Bit | Cables, Signals (Wi-Fi, Fiber). | **Hardware Intervention:** Wiretapping, Signal Jamming, Evil Twin (Fake Wi-Fi). |

---

## 2. THE TCP/IP PROTOCOL SUITE: THE LANGUAGE OF THE INTERNET

OSI is theoretical; TCP/IP is practical. The modern internet runs on this protocol stack.

### 🔥 The Three-Way Handshake
TCP is reliable because it "shakes hands" before speaking. Scanners like Nmap manipulate this handshake to perform reconnaissance.



1.  **SYN (Synchronize):** Client asks Server: "Can we talk?" (Is the port open?)
2.  **SYN-ACK (Synchronize-Acknowledge):** If open, Server replies: "Yes, we can talk."
3.  **ACK (Acknowledge):** Client replies: "Okay, starting now," and the connection is established.

> **💡 Critical Insight:** If an attacker sends a `SYN`, receives a `SYN-ACK`, and sends an `RST` (Reset) instead of an `ACK`, they confirm the port is open without creating a completed connection log on the server. This is called a **SYN (Stealth) Scan**.

### 🚩 TCP Flags and Meanings
Flags determine the intent of a packet.
* **SYN:** Request to start a connection.
* **ACK:** Acknowledgment.
* **RST:** Abrupt connection termination (Error or Refusal).
* **FIN:** Graceful connection termination.
* **PSH:** Process data immediately without buffering.
* **URG:** Urgent data processing.

---

## 3. TCP vs UDP: CONNECTION LOGIC

| Feature | TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
| :--- | :--- | :--- |
| **Connection Type** | Connection-oriented. | Connectionless. |
| **Reliability** | Verifies data delivery (Waits for ACK). | Does not verify, "Fire & Forget". |
| **Speed** | Slower (Due to handshake and checks). | Very fast. |
| **Use Case** | Web (HTTP), Email (SMTP), Files (FTP). | Streaming, Gaming, DNS, VoIP. |
| **Security Risk** | State tracking consumes resources (SYN Flood). | Source address is easily spoofed (Spoofing & DDoS Amplification). |

---

## 4. CRITICAL PROTOCOLS AND ATTACK VECTORS

A cybersecurity professional must know these protocols and their weaknesses by heart.

### 🔹 ARP (Address Resolution Protocol) - Layer 2
* **Function:** Translates IP addresses to MAC addresses. Shouts "Who has 192.168.1.5?" (Broadcast).
* **Vulnerability:** No verification mechanism. Anyone can say "I have that IP!"
* **Attack:** **ARP Poisoning.** The attacker impersonates the modem/gateway, forcing all network traffic to pass through their machine (MITM).



### 🔹 DNS (Domain Name System) - Port 53 (UDP/TCP)
* **Function:** Translates `google.com` to an IP address.
* **Vulnerability:** Queries are unencrypted (by default).
* **Attack:**
    * **DNS Spoofing:** Redirecting users to fake banking sites.
    * **DNS Tunneling:** Hiding data inside DNS queries to bypass firewalls.
    * **Zone Transfer:** Downloading all subdomains from a misconfigured server.

### 🔹 DHCP (Dynamic Host Configuration Protocol) - Port 67/68 (UDP)
* **Function:** Automatically distributes IP addresses to devices on the network.
* **Attack:** **DHCP Starvation.** The attacker consumes all available IPs in the pool using fake MAC addresses. Then, they set up a **Rogue DHCP** server to hijack new clients.

### 🔹 SMB (Server Message Block) - Port 445 (TCP)
* **Function:** Windows file and printer sharing.
* **Critical:** The most common protocol used for Lateral Movement in Windows networks.
* **Exploit:** EternalBlue (The exploit used by WannaCry ransomware).

---

## 5. IP ADDRESSING AND SUBNETTING

Used to divide and manage networks.

* **IPv4:** 32-bit address (e.g., 192.168.1.1).
* **IPv6:** 128-bit address (Future standard, more secure but complex).
* **MAC Address:** Hardware physical address (Changeable - MAC Spoofing).

### CIDR (Classless Inter-Domain Routing)
Hackers use CIDR notation to define target scopes.
* `/24` -> 256 IP Addresses (Standard for Local Network scans).
* `/16` -> 65,536 IP Addresses (Large corporate internal networks).
* `/32` -> Single IP (Targeted attack on a specific server).

---

## 6. NETWORK DEVICES AND SECURITY ROLES

### 🛑 Firewall
* **Function:** Blocks or allows packets based on rules (IP, Port, Protocol).
* **Types:**
    * *Stateless:* Looks only at individual packets.
    * *Stateful:* Tracks the state of connections (Does this packet belong to an active session?).
    * *WAF (Web Application Firewall):* Inspects HTTP content (Blocks SQLi, XSS).

### 🔀 Switch vs Hub
* **Hub:** "Dumb" device. Sends received data to everyone (Broadcast). If you open `Wireshark` on a Hub network, you see everyone's passwords.
* **Switch:** "Smart" device. Sends data only to the destination MAC address. To sniff traffic on a Switch, you must perform **ARP Poisoning**.

### 🌐 Router
* **Function:** Connects different networks (e.g., Home LAN to the Internet).
* **NAT (Network Address Translation):** Allows 10 home devices to access the internet via a single public IP. Attackers cannot directly access internal devices from the outside (Port Forwarding is required).

---

## 🛠️ PROFESSIONAL NETWORK TOOLKIT

Essentials that must be in every network expert's arsenal:

1.  **Wireshark:** Network traffic sniffing and deep packet analysis.
2.  **Nmap:** Network mapping, port scanning, and vulnerability detection.
3.  **Netcat (nc):** The Swiss Army Knife of networking. Port listening, data transfer, reverse shells.
4.  **Tcpdump:** Command-line packet capture (for GUI-less servers).
5.  **Burp Suite:** Intercepting and manipulating Web traffic (HTTP Proxy).

---

### 🧠 FINAL WORD: THE MINDSET
> "Network security is not about building walls; it is about knowing how the doors work, where the windows lock, and where the ventilation shafts lead. You cannot protect a packet unless you understand its lifecycle."

---
*Last Updated: 2026-01-22*
*Status: Complete - Reference Document*