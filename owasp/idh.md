# Insecure Data Handling — OWASP 2025

## 🔐 A04 — Cryptographic Failures

**What it is:** Sensitive data is exposed because encryption is absent, incorrectly implemented, or built on outdated, broken algorithms. The failure isn't always "no encryption" — it's often *wrong* encryption that gives a false sense of security.

> **Key distinction:** Encryption and hashing are different tools for different jobs. Encryption is reversible (with a key). Hashing is one-way. **Passwords should always be hashed, never encrypted** — if an encryption key is stolen, all encrypted passwords are immediately recoverable. A strong hash requires brute-forcing each one individually.

### Common Failure Patterns

| Pattern | Why It's Dangerous |
|---------|-------------------|
| **Passwords stored in plaintext** | A database breach exposes every user's password instantly |
| **Weak hashing algorithms** — MD5, SHA-1 | Collision-broken and trivially reversible with rainbow tables — not suitable for any security use |
| **Weak encryption** — DES, 3DES, ECB mode | DES/3DES are computationally broken; ECB mode leaks data patterns even when "encrypted" |
| **Hard-coded credentials or keys** | Secrets in source code are exposed to anyone with repo access — and persist forever in git history |
| **No encryption in transit** | HTTP traffic can be intercepted — credentials, tokens, and sensitive data exposed via MITM |
| **No encryption at rest** | A storage breach directly exposes all plaintext data |
| **Poor key management** | Keys stored alongside the data they protect, never rotated, or shared insecurely |

### Prevention

| Recommendation | Detail |
|----------------|--------|
| **Hash passwords correctly** | Use bcrypt, scrypt, or Argon2 — these are slow by design, making brute force expensive |
| **Use modern encryption algorithms** | AES-256-GCM or ChaCha20-Poly1305 for data at rest; TLS 1.3 for data in transit |
| **Never hard-code secrets** | Use environment variables or a secrets manager (Azure Key Vault, AWS KMS, HashiCorp Vault) |
| **Rotate keys and secrets regularly** | Revoke immediately on suspected compromise; follow defined cryptographic periods |
| **Encrypt sensitive data at rest** | Databases, backups, and file stores containing PII or credentials should be encrypted |
| **Enforce TLS with valid certificates** | Disable TLS 1.0/1.1 and weak cipher suites; use certificates from a trusted CA |

> **Why bcrypt over SHA-256 for passwords?** General-purpose hash functions like SHA-256 are designed to be *fast* — a GPU can compute billions per second. bcrypt, scrypt, and Argon2 are intentionally slow and memory-hard, making large-scale brute force attacks impractical even if the hash database is stolen.

---

## 💉 A05 — Injection

**What it is:** An application mishandles user input and passes it unsanitised into a system that interprets and executes it — such as a database, operating system shell, or template engine. The attacker's input is treated as a command rather than data.

> **Root cause:** The application fails to maintain a clear boundary between **data** and **instructions**. When user-supplied strings are concatenated directly into queries or commands, the interpreter cannot tell where the developer's logic ends and the attacker's payload begins.

### Injection Types

| Type | Target | Example Payload | Impact |
|------|--------|----------------|--------|
| **SQL Injection** | Database query | `' OR '1'='1` appended to a login query | Bypass authentication, dump entire database, delete records |
| **Command Injection** | OS shell | `; rm -rf /` appended to a filename input | Execute arbitrary OS commands with the app's privileges |
| **Server-Side Template Injection (SSTI)** | Template engine | `{{7*7}}` rendered as `49` — confirms code execution | Remote code execution via the template engine (Jinja2, Twig, etc.) |
| **AI Prompt Injection** | LLM / AI agent | Malicious instructions embedded in user input that override system prompts | Hijack model behaviour, extract hidden prompts, trigger unintended actions |
| **LDAP Injection** | Directory service | Manipulated filter strings in LDAP queries | Bypass auth, enumerate users, expose directory data |
| **XPath Injection** | XML data store | Modified XPath expressions | Bypass auth, extract XML data unauthorised |

### Prevention

| Principle | How to Apply It |
|-----------|----------------|
| **Treat all user input as untrusted** | Never assume input is safe — validate, sanitise, or reject anything unexpected |
| **Use parameterised queries / prepared statements** | Pass input as a typed parameter, not as part of the query string — the database never interprets it as SQL |
| **Use an ORM** | Object-relational mappers (e.g. SQLAlchemy, Hibernate) abstract queries and handle parameterisation by default |
| **Avoid shell-executing functions** | Don't pass user input to functions like `exec()`, `system()`, `popen()` — if OS commands are needed, use parameterised subprocess calls |
| **Validate against an allowlist** | Define exactly what valid input looks like — reject anything that doesn't match, rather than trying to block known bad patterns |
| **Apply context-aware output encoding** | Encode output for its destination — HTML encoding for web pages, JSON encoding for APIs |
| **Sandbox AI inputs and outputs** | Never pass raw user input directly into a system prompt; validate and filter model outputs before acting on them |

> **Parameterised queries vs string concatenation:**
> ```sql
> -- ❌ Vulnerable — input is interpreted as SQL
> "SELECT * FROM users WHERE username = '" + userInput + "'"
>
> -- ✅ Safe — input is treated as a literal value, never executed
> "SELECT * FROM users WHERE username = ?" with parameter [userInput]
> ```

---

## 🔗 A08 — Software and Data Integrity Failures

**What it is:** An application implicitly trusts code, updates, or data without verifying their origin, integrity, or authenticity. This creates an attack surface where an adversary can substitute, tamper with, or inject malicious content — and the application will accept and execute it without question.

> **The core assumption that gets broken:** "This update / script / config file came from where I think it came from, and nobody has changed it." Without verification, that assumption is a vulnerability.

> **Relationship to Supply Chain (A03):** A03 covers compromised dependencies. A08 covers the failure to *verify* what you receive — they often overlap. The SolarWinds attack is an example of both: a compromised build process (A03) delivered an update that was accepted without integrity verification (A08).

### What Falls Under This Category

| Scenario | Description |
|----------|-------------|
| **Trusting software updates without verification** | Installing packages or updates without checking their cryptographic signature |
| **Loading scripts from untrusted sources** | CDN-hosted JavaScript included with no Subresource Integrity (SRI) check |
| **Deserialising untrusted data** | Accepting serialised objects (JSON, XML, binary) from external sources without validation — can lead to remote code execution |
| **Unverified CI/CD pipeline inputs** | Build artifacts, config files, or dependencies pulled from untrusted or unverified sources during deployment |
| **Accepting binaries or templates without integrity checks** | Running downloaded executables or rendering templates without confirming they haven't been tampered with |
| **AI model loading without provenance checks** | Loading a model checkpoint from an unverified source — poisoned models can embed backdoors |

### Prevention

| Method | Description |
|--------|-------------|
| **Cryptographic checksums** | Verify the hash (SHA-256 or stronger) of any file, update, or artifact before using it — even from trusted sources |
| **Code signing** | Require software updates and packages to carry a valid digital signature from a known key |
| **Subresource Integrity (SRI)** | Add `integrity="sha384-..."` attributes to CDN-hosted `<script>` and `<link>` tags — browsers will refuse to load content that doesn't match |
| **Establish trust boundaries** | Define clearly what sources are trusted for each type of content — code, data, models, config — and enforce those boundaries strictly |
| **Never assume legitimacy** | Treat all externally sourced code, updates, and data as untrusted until their integrity has been independently verified |
| **Secure and audit CI/CD pipelines** | Sign and verify build artifacts at every stage — detect tampering before it reaches production |
| **Validate deserialised data** | Never deserialise data from untrusted sources without schema validation; prefer safer formats over native object serialisation |

> **Subresource Integrity example:**
> ```html
> <!-- ❌ Trusts the CDN completely — if the CDN is compromised, so are your users -->
> <script src="https://cdn.example.com/library.js"></script>
>
> <!-- ✅ Browser verifies the hash before executing — tampering causes a load failure -->
> <script src="https://cdn.example.com/library.js"
>         integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
>         crossorigin="anonymous"></script>
> ```
