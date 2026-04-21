# Application Design Failures — OWASP 2025

## 🔧 A02 — Security Misconfigurations

**What it is:** Systems, servers, or applications deployed with unsafe defaults, incomplete settings, or unnecessarily exposed services. Misconfigurations don't require a sophisticated attack — they hand attackers easy entry points that should never have existed.

> **Key distinction:** This is not a code vulnerability. It's an operational failure — something was left open, unchanged, or unreviewed during deployment or maintenance.

> **Real-world example (2017 — Uber):** A backup S3 bucket containing sensitive driver and rider data was left publicly accessible with no credentials required. Attackers downloaded the data directly. A deployment oversight caused a breach affecting 57 million users.

### Common Patterns

| Pattern | Description | Real-World Form |
|---------|-------------|----------------|
| **Default credentials** | Factory-set usernames/passwords left unchanged | `admin/admin` on routers, databases, Jenkins, WordPress |
| **Exposed unnecessary services** | Internal or debug endpoints reachable from the internet | Admin panels, phpMyAdmin, debug APIs, Swagger UI |
| **Misconfigured cloud storage** | Buckets or blobs set to public read or write | S3, Azure Blob, GCP Cloud Storage with no access policy |
| **Unrestricted API access** | Endpoints accessible without authentication or authorisation | Internal APIs callable directly from the internet |
| **Verbose error messages** | Stack traces expose framework, paths, and database details | `NullPointerException at /app/routes/user.py line 42` |
| **Outdated software** | Unpatched frameworks, containers, or libraries with known CVEs | Running Log4j 2.14, outdated WordPress plugins |
| **Exposed AI/ML endpoints** | Model inference APIs accessible without access controls | Public `/predict` endpoints leaking model behaviour |

### Prevention

- Harden defaults and remove all unused features, services, and endpoints before deployment
- Enforce least privilege and strong authentication across all systems and cloud resources
- Limit network exposure — internal services should never be internet-facing without justification
- Audit cloud storage permissions regularly — treat public buckets as critical misconfigurations
- Strip stack traces and system information from all error responses in production
- Keep all software, frameworks, and containers patched and up to date
- Integrate automated configuration scanning into CI/CD pipelines — catch misconfigs before they ship
- Apply access controls and monitoring to all AI and automation endpoints

---

## 📦 A03 — Software Supply Chain

**What it is:** Applications depend on external components — libraries, frameworks, services, AI models — that may be compromised, outdated, or improperly verified. A single malicious dependency can compromise an entire system without any flaw in your own code.

> **Why it's dangerous:** Supply chain attacks are indirect — attackers don't target your application, they target something your application trusts. Once inside that trust chain, they inherit all the access your system grants to that dependency.

> **Real-world example (2021 — SolarWinds Orion):** Attackers inserted malicious code into a trusted software update. Thousands of organisations — including US government agencies — automatically installed it. The flaw wasn't in SolarWinds' core logic. It was in the build, signing, verification, and distribution process. The breach went undetected for months.

### Common Patterns

| Pattern | Risk |
|---------|------|
| **Unverified or unmaintained dependencies** | Libraries with known CVEs or silently replaced by malicious forks (typosquatting) |
| **Auto-installing updates without verification** | A compromised update reaches production automatically and silently |
| **Unaudited third-party AI models** | Models from unverified sources may contain backdoors or adversarial behaviours |
| **Insecure CI/CD pipelines** | Build or deploy process can be tampered with to inject malicious code |
| **No provenance or license tracking** | No visibility into where a component came from or who maintains it |
| **No post-deployment monitoring** | Vulnerabilities discovered after deployment go unaddressed until exploited |

### Prevention

- Verify all third-party components, libraries, and AI models before use — check signatures and hashes
- Use a Software Bill of Materials (SBOM) to maintain a complete inventory of dependencies
- Monitor and patch dependencies continuously — not just at deployment time
- Sign and verify all software packages and updates before installation
- Lock down CI/CD pipelines — restrict who can modify build scripts and deployment configs
- Track provenance and licensing for all components — know who maintains what
- Include supply chain threat modelling in the SDLC from design through to deployment
- Implement runtime monitoring for unusual behaviour from dependencies or AI components

---

## 🔐 A04 — Cryptographic Failures

**What it is:** Encryption is used incorrectly, with weak algorithms, or not at all. This includes hard-coded secrets, poor key management, and sensitive data transmitted or stored in plaintext. When cryptographic controls fail, sensitive data is exposed — even if every other control is working correctly.

> **Key distinction:** This is not always about "no encryption" — it's often about *wrong* encryption. An application can use encryption and still be vulnerable if the algorithm is broken, the keys are mismanaged, or the implementation has flaws.

### Common Patterns

| Pattern | Why It's Dangerous |
|---------|-------------------|
| **Weak algorithms** — MD5, SHA-1, ECB mode | MD5 and SHA-1 are collision-broken; ECB mode leaks data patterns even when encrypted |
| **Hard-coded secrets** | Secrets in source code are exposed to anyone with repo access — and live forever in git history |
| **Poor key rotation** | Old or compromised keys remain valid indefinitely — one breach exposes all past data |
| **No encryption at rest** | A database breach exposes all plaintext sensitive data directly |
| **No encryption in transit** | HTTP traffic can be intercepted — credentials, tokens, and data exposed via MITM |
| **Invalid or self-signed TLS certificates** | Clients may be trained to ignore warnings, enabling MITM attacks |
| **AI/ML systems exposing sensitive inputs** | Model parameters, embeddings, or sensitive prompts transmitted without encryption |

### Prevention

| Recommendation | Detail |
|----------------|--------|
| **Use modern symmetric encryption** | AES-256-GCM or ChaCha20-Poly1305 for data at rest and in transit |
| **Enforce TLS 1.3** | Disable TLS 1.0, 1.1, and weak cipher suites; use valid certificates from a trusted CA |
| **Use a key management service** | Azure Key Vault, AWS KMS, or HashiCorp Vault — never hard-code secrets in source code |
| **Rotate keys and secrets regularly** | Follow defined cryptographic periods; revoke immediately on suspected compromise |
| **Maintain a certificate and key inventory** | Know what you own, who owns it, and when it expires |
| **Hash passwords correctly** | Use bcrypt, scrypt, or Argon2 — never MD5 or SHA-1 for password storage |
| **Protect AI inputs and outputs** | Ensure model parameters, embeddings, and sensitive prompts are encrypted in transit and at rest |

> **Password hashing vs encryption:** Passwords should be *hashed*, not *encrypted*. Encryption is reversible — if the key is compromised, all passwords are exposed. A strong hash (bcrypt, Argon2) is one-way and computationally expensive to crack.

---

## 🏗️ A06 — Insecure Design

**What it is:** Flawed logic or architecture is built into a system from the start — through skipped threat modelling, absent security requirements, or unchecked assumptions about how users (or AI) will behave. Unlike misconfiguration, insecure design **cannot be patched** — it requires rethinking and rebuilding the system.

> **Real-world example — Clubhouse:** The app's backend API had no authentication — anyone could directly query user data, room info, and private conversations without using the mobile app. The "private conversation" promise was an illusion built on a broken foundation. The entire privacy model collapsed because it was never designed to be enforced server-side.

> **The core problem:** Developers design for the happy path — the legitimate, well-behaved user. Attackers design for every other path. Without adversarial thinking built into the design process, those other paths go unguarded.

### Common Insecure Design Patterns

| Pattern | Description |
|---------|-------------|
| **Weak business logic controls** | Recovery flows, approval chains, or state transitions that can be abused or skipped |
| **Flawed assumptions about user behaviour** | Assuming users will only interact through the intended interface (e.g. the mobile app) |
| **AI components with unchecked authority** | LLMs or agents that can take actions — generate queries, write code, classify users — without limits or human oversight |
| **Missing LLM guardrails** | No input/output filtering, no prompt injection protections, no output validation |
| **Test/debug bypasses in production** | Developer shortcuts (e.g. `?debug=true`, hardcoded test accounts) that become live attack vectors |
| **No abuse-case review** | Security requirements exist on paper but are never tested against adversarial scenarios |

### AI-Specific Design Risks (2025)

| Risk | Description |
|------|-------------|
| **Prompt injection** | User input blends with system prompts — attacker hijacks the model's context, extracts hidden instructions, or redirects actions |
| **Blind trust in model output** | System acts on AI decisions without validation or human review — fragile and exploitable |
| **Poisoned models** | Models from unverified sources or fine-tuned on unsafe data embed hidden behaviours, biases, or backdoors |
| **Unchecked agent authority** | Automation agents with excessive permissions can be manipulated into taking destructive or unauthorised actions |
| **Inference attacks** | Querying a model repeatedly to extract training data or reconstruct sensitive inputs |

### How to Design Securely

**General principles:**
- Build threat modelling into **every stage** of development — not just the design phase
- Define explicit security requirements for each feature before implementation begins
- Design for adversarial users, not just legitimate ones — think: how would an attacker abuse this?
- Apply least privilege across all users, APIs, services, and AI agents
- Ensure authentication, authorisation, and session management are enforced server-side, consistently
- Fail safe by default — on error, deny access rather than grant it

**AI-specific principles:**
- Treat every external model as untrusted until proven otherwise
- Validate and filter all model inputs and outputs — never pass raw user input directly into a prompt
- Separate system prompts from user-controlled content — enforce strict boundaries
- Keep sensitive data out of prompts unless absolutely necessary; apply strict access controls when required
- Require human review for any high-risk or irreversible AI-driven action
- Log model provenance, monitor runtime behaviour, and apply differential privacy for sensitive training data
- Include AI threat modelling — covering prompt injection, agent misuse, inference risks, and supply chain — throughout the SDLC
