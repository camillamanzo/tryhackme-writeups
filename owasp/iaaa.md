# IAAA

## 🔐 IAAA — Identity, Authorisation, Authentication & Accountability

Before diving into the vulnerabilities, it helps to understand the four pillars these failures break.

| Pillar | Description | Example |
|--------|-------------|---------|
| **Identity** | A unique account representing a person or service | Email address, user ID, API key |
| **Authentication** | Proving you are who you claim to be | Password, OTP, biometric |
| **Authorisation** | What an authenticated identity is allowed to do | Role-based access, permissions |
| **Accountability** | Recording and alerting on who did what, when, and from where | Audit logs, SIEM alerts |

---

## A01 — Broken Access Control 🚪

**What it is:** The server fails to properly enforce authorisation — users can access resources or perform actions they shouldn't be allowed to.

### Attack Patterns

| Pattern | Description | Example |
|---------|-------------|---------|
| **IDOR** (Insecure Direct Object Reference) | Manipulating a reference to access another user's data | Changing `?id=6` to `?id=7` in the URL reveals another user's record |
| **Horizontal Privilege Escalation** | Same role, different user's data | User A accesses User B's account without elevated permissions |
| **Vertical Privilege Escalation** | Gaining higher-privilege access than assigned | A regular user performs admin-only actions |

> **Root cause:** Access control is enforced client-side (UI hides buttons) rather than server-side — the server itself never validates whether the requesting user is authorised.

---

## A07 — Authentication Failures 🔑

**What it is:** The application cannot reliably verify and bind a user's identity — authentication mechanisms are weak, bypassable, or broken.

### Common Issues

| Weakness | Description |
|----------|-------------|
| **Username enumeration** | Different error messages for valid vs invalid usernames allow attackers to confirm which accounts exist |
| **Weak passwords** | No complexity requirements, no lockout, no rate limiting — brute force is trivial |
| **Logic flaws** | Bugs in login or registration flows that allow bypassing authentication entirely |
| **Insecure session handling** | Session tokens exposed in URLs, not invalidated on logout, or guessable |
| **Insecure cookie handling** | Cookies missing `HttpOnly`, `Secure`, or `SameSite` flags |

> **Impact:** Broken authentication is the gateway to every other attack — if an attacker can authenticate as another user (especially an admin), all other controls may be irrelevant.

---

## A09 — Logging and Alerting Failures 📋

**What it is:** Applications fail to record or alert on security-relevant events — leaving defenders blind to ongoing attacks.

**Accountability** is the IAA pillar this breaks — without logs, there is no record of who did what.

### What Should Be Logged

| Event | Why It Matters |
|-------|---------------|
| Failed login attempts | Detect brute force and credential stuffing |
| Privilege escalation | Detect lateral movement and insider threats |
| Access to sensitive data | Detect exfiltration or unauthorised access |
| Configuration changes | Detect tampering or persistence mechanisms |
| Errors and exceptions | Surface exploitation attempts and crashes |

> **The goal isn't just logging — it's alerting.** Logs that are never reviewed or fed into a SIEM provide no defensive value. Detection requires both collection and monitoring.

---

## ⚙️ Application Design Failures

---

## A02 — Security Misconfigurations 🔧

**What it is:** Systems, servers, or applications deployed with unsafe defaults, incomplete settings, or unnecessarily exposed services. One of the most common and easily preventable vulnerability classes.

> **Real-world example (2017):** Uber left an S3 backup bucket publicly accessible with no credentials required — attackers downloaded sensitive driver and rider data directly. A deployment mistake caused a significant breach.

### Common Patterns

| Pattern | Example |
|---------|---------|
| Default credentials | Admin/admin left unchanged on routers, databases, or CMS platforms |
| Exposed unnecessary services | Debug endpoints, admin panels, or internal APIs reachable from the internet |
| Misconfigured cloud storage | S3 buckets, Azure Blob, or GCP buckets set to public read |
| Missing authentication on APIs | Endpoints accessible without any auth token or key |
| Verbose error messages | Stack traces exposing file paths, framework versions, or database structure |
| Outdated software | Unpatched frameworks, containers, or libraries with known CVEs |
| Exposed AI/ML endpoints | Model inference endpoints accessible without access controls |

### Prevention

- Harden defaults and remove unused features, services, and endpoints
- Enforce least privilege and strong authentication across all systems
- Limit network exposure and segment sensitive resources
- Keep software, frameworks, and containers up to date
- Strip stack traces and system info from error responses
- Audit cloud storage permissions regularly
- Integrate automated configuration checks into CI/CD pipelines

---

## A03 — Software Supply Chain 📦

**What it is:** Applications rely on components, libraries, services, or models that are compromised, outdated, or improperly verified. A single malicious dependency can compromise an entire system — without touching your own code.

> **Real-world example (2021):** The SolarWinds Orion compromise — attackers inserted malicious code into a trusted software update. Thousands of organisations automatically installed it. The flaw wasn't in SolarWinds' core logic — it was in the build, verification, and distribution process.

### Common Patterns

| Pattern | Risk |
|---------|------|
| Unverified or unmaintained dependencies | Libraries with known CVEs or malicious forks |
| Auto-installing updates without verification | A compromised update silently reaches production |
| Over-reliance on third-party AI models | Models pulled from unverified sources may contain backdoors |
| Insecure CI/CD pipelines | Tampering during build or deployment injects malicious code |
| No provenance or license tracking | No visibility into where components come from |
| No post-deployment monitoring | Vulnerabilities discovered after deployment go unaddressed |

### Prevention

- Verify all third-party components, libraries, and AI models before use
- Sign, verify, and audit software updates and packages
- Lock down CI/CD pipelines to prevent tampering
- Monitor and patch dependencies continuously — not just at deployment
- Track provenance and licensing for all components
- Include supply chain threat modelling in the SDLC

---

## A04 — Cryptographic Failures 🔐

**What it is:** Encryption is used incorrectly, weakly, or not at all — including weak algorithms, hard-coded keys, poor key management, or sensitive data transmitted in plaintext.

### Common Patterns

| Pattern | Why It's Dangerous |
|---------|-------------------|
| Weak algorithms (MD5, SHA-1, ECB mode) | Broken — trivially reversible or collision-prone |
| Hard-coded secrets in source code | Secrets committed to repos are exposed to anyone with access |
| Poor key rotation | Old compromised keys remain valid indefinitely |
| No encryption at rest | Database breach exposes plaintext sensitive data |
| Invalid/self-signed TLS certificates | Opens the door to MITM attacks |
| AI/ML systems exposing model parameters | Sensitive inputs or embeddings transmitted without encryption |

### Prevention

| Recommendation | Detail |
|----------------|--------|
| Use modern algorithms | AES-GCM, ChaCha20-Poly1305 for symmetric; TLS 1.3 for transport |
| Use a key management service | Azure Key Vault, AWS KMS, HashiCorp Vault — never hard-code secrets |
| Rotate keys and secrets regularly | Follow defined cryptographic periods |
| Maintain a certificate/key inventory | Know who owns what, when it expires |
| Enforce encryption at rest and in transit | No plaintext sensitive data anywhere in the stack |

---

## A06 — Insecure Design 🏗️

**What it is:** Flawed logic or architecture is built into a system from the start — through skipped threat modelling, absent design requirements, or unchecked assumptions about how users (or AI) will behave. Unlike misconfiguration, insecure design cannot be fixed by patching — it requires redesigning the system.

> **Real-world example:** Clubhouse's early backend API had no authentication — anyone could query user data, room info, and private conversations directly, bypassing the mobile app entirely. The "private conversation" model was an illusion built on a broken foundation.

### Common Insecure Design Patterns (2025)

| Pattern | Description |
|---------|-------------|
| Weak business logic controls | Recovery flows, approval chains, or state transitions that can be abused |
| Flawed assumptions about user behaviour | Designing only for the happy path — not adversarial users |
| AI components with unchecked authority | LLMs or agents that can take actions without limits or oversight |
| Missing LLM guardrails | No input/output filtering, no prompt injection protections |
| Test/debug bypasses left in production | Developer shortcuts that become attack vectors |
| No abuse-case review | Security requirements are defined but never tested against adversarial scenarios |

### AI-Specific Design Risks in 2025

| Risk | Description |
|------|-------------|
| **Prompt injection** | User input blends with system prompts — attacker hijacks context or extracts hidden data |
| **Blind trust in model output** | Acting on AI decisions without validation creates fragile, exploitable systems |
| **Poisoned models** | Models pulled from unverified sources or fine-tuned on unsafe data embed hidden behaviours or backdoors |
| **Unchecked agent authority** | Automation agents with excessive permissions can be manipulated to take destructive actions |

### How to Design Securely

- Build threat modelling into **every stage** of development — not just at the start
- Define clear security requirements for each feature before implementation
- Apply least privilege across users, APIs, services, and AI agents
- Treat every model as untrusted — validate and filter all inputs and outputs
- Separate system prompts from user content; keep sensitive data out of prompts
- Require human review for high-risk AI actions
- Log model provenance, monitor behaviour, and apply differential privacy for sensitive data
- Continuously test for logic flaws, abuse paths, and emergent risks as new components are added
