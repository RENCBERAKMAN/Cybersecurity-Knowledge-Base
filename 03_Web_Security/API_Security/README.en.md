<div align="center">

<img src="https://capsule-render.vercel.app/render?type=waving&color=gradient&height=280&section=header&text=API%20SECURITY%20ARSENAL&fontSize=42&animation=fadeIn&fontColor=ffffff&desc=Breaking%20the%20Machine-to-Machine%20Trust&descFontSize=20&gradient=00FFFF,FF00FF" />

<br>

<img src="https://img.shields.io/badge/DOMAIN-API_HACKING-blueviolet?style=for-the-badge&logo=postman" />
<img src="https://img.shields.io/badge/STANDARD-OWASP_API_TOP_10-green?style=for-the-badge&logo=owasp" />
<img src="https://img.shields.io/badge/PROTOCOL-REST_&_GRAPHQL-black?style=for-the-badge&logo=graphql" />
<img src="https://img.shields.io/badge/AUTH-JWT_&_OAUTH2-orange?style=for-the-badge&logo=json" />

</div>

<br>

> **"An API is the application without the mask. There is no HTML to hide the logic, no CSS to dress up the data. It is a direct pipe to the database. We do not hack the interface; we hack the business logic itself."**

<br>

## 🧭 NAVIGATION

| [1. The Genesis: Why APIs Bleed](#1-the-genesis-why-apis-bleed) | [2. The Anatomy: REST, GraphQL & JWT](#2-the-anatomy-rest-graphql--jwt) | [3. The OWASP API Top 10 Logic](#3-the-owasp-api-top-10-logic) | [4. The Supreme Arsenal (Attack Table)](#-the-supreme-arsenal) |
| :---: | :---: | :---: | :---: |

<br>

---

# 🇺🇸 ENGLISH: TECHNICAL SUPREMACY

### 🧠 1. THE GENESIS: WHY APIS BLEED
To understand API Hacking, you must understand **Modern Architecture**.
In the past, the server sent HTML (Monolithic). Today, the server sends **Raw Data (JSON)**, and the client (React, Mobile App) builds the view.

* **The Exposure:** Because the client needs *data* to build the UI, APIs often send **more data than necessary** (PII, credit cards), relying on the frontend developer to hide it. We, as hackers, ignore the frontend and look at the raw JSON response.
* **The Trust Issue:** APIs are designed for machines. Developers assume that "if the request comes from my Mobile App, it is valid." They forget that **Requests can be replayed and modified** outside the app (via Burp Suite or Postman).

### ⚙️ 2. THE ANATOMY: REST, GRAPHQL & JWT
We are not attacking "pages"; we are attacking **Endpoints** and **State**.

#### A. REST (Representational State Transfer)
* **Structure:** Uses standard HTTP verbs (`GET`, `POST`, `PUT`, `DELETE`) on resources (`/users/105`).
* **The Flaw:** Trusting the ID. If I change `PUT /users/105` to `PUT /users/106`, does the API check if I *own* user 106? (This is **BOLA**).

#### B. GraphQL (The Query Language)
* **Structure:** One endpoint (`/graphql`). The client asks exactly for what it wants.
* **The Flaw:** **Introspection**. If enabled, we can ask the API: *"Tell me your entire database schema, all fields, and all hidden arguments."* It’s like getting the map of the bank vault from the bank manager.


#### C. JWT (JSON Web Tokens) - The Stateless Key
Instead of sessions, APIs use JWTs (`Header.Payload.Signature`).
* **Atomic Logic:** The server signs the data with a secret key.
* **The Hack:**
    * **None Algorithm:** We tell the server "I am signed with *nothing*". Some libraries accept this.
    * **Key Confusion:** We force the server to verify the signature using its *Public Key* as a *HMAC Secret*.


---

### 💀 3. THE OWASP API TOP 10 LOGIC (2023 EDITION)

The bible of API exploitation. These are not bugs; they are architectural failures.

#### API1: Broken Object Level Authorization (BOLA)
* **Concept:** IDOR on steroids.
* **The Attack:** `GET /receipts/500` -> `GET /receipts/501`.
* **Why it happens:** The code checks "Is the user logged in?" but fails to check "Does this user **own** receipt 501?"

#### API2: Broken Authentication
* **Concept:** Weak protection of the entry door.
* **The Attack:** Stuffing credentials, using weak JWT secrets, or missing rate limiting on `/login`.

#### API3: Broken Object Property Level Authorization (Mass Assignment)
* **Concept:** The framework binds client input directly to internal objects.
* **The Attack:** The user sends `{"username": "hacker", "role": "ADMIN"}`. Even if the UI doesn't show the "role" field, the API might blindly accept it and update the database privilege.

#### API4: Unrestricted Resource Consumption
* **Concept:** Denial of Service (DoS) via logic.
* **The Attack:** Sending a GraphQL query nested 1000 levels deep, or requesting `page_size=1000000`. The server exhausts CPU/RAM trying to fulfill the request.

#### API5: Broken Function Level Authorization (BFLA)
* **Concept:** Finding hidden admin buttons.
* **The Attack:** A regular user changes the method from `GET /users` to `DELETE /users`, or guesses a URL like `/api/v1/admin/export_all`.

---

# 🛠️ THE SUPREME ARSENAL
*A definitive guide to weaponizing API logic.*

| Technique / Tool | Atomic Mechanism | Educational Logic | 💀 Strategic Adversary Objective |
| :--- | :--- | :--- | :--- |
| **BOLA / IDOR Hunting** | ID Parameter Manipulation. | Changing numerical (`id=10`) or UUID inputs in API calls to access other users' data. | **Data Exfiltration:** Dumping the entire database record by record by iterating numbers. |
| **Mass Assignment** | JSON Property Injection. | Injecting fields like `"is_admin": true` or `"balance": 99999` into a POST/PUT request. | **Privilege Escalation:** Becoming an administrator by "guessing" the internal variable name. |
| **JWT None Algo** | Header Manipulation (`alg: None`). | Stripping the signature section and telling the server "No crypto needed." | **Authentication Bypass:** Logging in as `{"sub": "admin"}` without knowing the secret key. |
| **GraphQL Introspection** | Schema Querying (`__schema`). | Asking the API to return its entire internal documentation and type definitions. | **Reconnaissance:** Discovering hidden "Shadow APIs" or deprecated fields that are vulnerable. |
| **SSRF via Webhooks** | Outbound Request Triggering. | Setting a user profile picture URL to `http://169.254.169.254` (Cloud Metadata). | **Cloud Takeover:** Forcing the API to steal its own AWS/Azure credentials. |
| **Race Conditions** | Parallel Request Execution. | Sending 10 "Redeem Coupon" requests simultaneously before the DB updates the balance. | **Business Logic Fraud:** Using a single-use coupon multiple times or withdrawing money you don't have. |
| **Verb Tampering** | HTTP Method Switching. | Changing `GET /users` to `HEAD /users` or `PUT /users` to bypass ACLs. | **ACL Bypass:** Accessing resources restricted on GET but accidentally open on other methods. |
| **Kiterunner** | Context-Aware Discovery. | Brute-forcing API routes using wordlists derived from real-world API specs (Swagger/OpenAPI). | **Shadow API Discovery:** Finding `/api/v1/mobile/hidden_test` endpoints that developers forgot to delete. |
| **Parameter Pollution** | Duplicate Key Injection. | Sending `id=123&id=456`. The WAF checks the first, the Application processes the second. | **WAF Bypass:** Sneaking malicious payloads past security filters. |
| **BOLA in GraphQL** | Nested Object Traversal. | Querying `{ user { posts { comments { author { email } } } } }`. | **Deep Data Mining:** extracting PII from related objects that shouldn't be accessible. |

<br>

### 🛡️ DEFENSE: THE IMMUTABLE CONTRACT
1.  **Schema Validation:** Reject any JSON field that is not explicitly defined in the OpenAPI spec (Stops Mass Assignment).
2.  **Object-Level Checks:** Every database query must include `WHERE owner_id = current_user_id`.
3.  **Rate Limiting:** Limit not just by IP, but by User ID and specific Endpoint (Stops Brute Force/DoS).

> **"The API is the brain. If you control the inputs to the brain, you control the reality of the machine."**

---
**Status:** `SUPREME_API_REFERENCE`
**Revision:** 1.0 (Jan 2026)
**Author:** Principal Security Researcher