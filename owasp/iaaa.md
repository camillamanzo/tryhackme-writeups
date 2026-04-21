# IAAA Failures — Identity, Authentication, Authorisation & Accountability

## 🧩 The IAAA Framework

IAAA describes the four sequential steps that make up secure access control. Each step builds on the previous — if any one fails, the entire chain can collapse.

| Step | Pillar | Description | Example |
|------|--------|-------------|---------|
| 1 | **Identity** | A unique account representing a person or service | Email address, user ID, service account, API key |
| 2 | **Authentication** | Proving you are who you claim to be | Password, OTP, biometric, certificate |
| 3 | **Authorisation** | Defining what an authenticated identity is allowed to do | Role-based access, file permissions, API scopes |
| 4 | **Accountability** | Recording and alerting on who did what, when, and from where | Audit logs, SIEM alerts, access trails |

> **The chain matters:** Identity without authentication is trust without proof. Authentication without authorisation is access without limits. Authorisation without accountability is action without consequence. All four must work together.

---

## A01 — Broken Access Control 🚪

**What it is:** The server fails to properly enforce **authorisation** — users can access data or perform actions they shouldn't be permitted to.

> **Root cause:** Access control is enforced client-side (e.g. buttons are hidden in the UI) but not server-side — the server never validates whether the requesting user actually has permission. Removing the UI restriction, or crafting a direct request, bypasses the control entirely.

### Attack Patterns

| Attack | Description | Example |
|--------|-------------|---------|
| **IDOR** (Insecure Direct Object Reference) | A user manipulates a reference in a request to access another user's resource | Changing `?id=6` to `?id=7` in the URL reveals another user's private data |
| **Horizontal Privilege Escalation** | Same permission level, but accessing another user's data or resources | User A reads or modifies User B's account without needing elevated privileges |
| **Vertical Privilege Escalation** | Gaining access to a higher permission level than assigned | A regular user triggers admin-only functionality (e.g. deleting users, changing roles) |
| **Forced Browsing** | Directly navigating to URLs or API endpoints that are not linked in the UI but still accessible | Accessing `/admin/dashboard` directly despite not being an admin |
| **JWT/Token Manipulation** | Modifying an authentication token to change roles or user IDs | Editing a JWT payload from `"role":"user"` to `"role":"admin"` if signature is not verified |

### Prevention

- Enforce access control **server-side** on every request — never trust the client
- Deny by default — access requires an explicit grant, not the absence of a denial
- Use a centralised authorisation mechanism rather than scattered per-route checks
- Log access control failures and alert on repeated violations
- Rate-limit and monitor API endpoints — especially those that accept user-controlled IDs

---

## A07 — Authentication Failures 🔑

**What it is:** The application cannot reliably verify and bind a user's identity — authentication mechanisms are weak, inconsistent, or bypassable.

> **Why it matters:** Authentication is the gatekeeper. If it fails, all other controls (authorisation, logging, session management) are built on a broken foundation. A successful authentication bypass often grants full account access.

### Common Weaknesses

| Weakness | Description | Risk |
|----------|-------------|------|
| **Username enumeration** | Different responses for valid vs invalid usernames reveal which accounts exist | Enables targeted brute force and credential stuffing |
| **No rate limiting or lockout** | Unlimited login attempts allowed without slowdown or lockout | Makes brute force and password spraying trivial |
| **Weak password policy** | No minimum length, complexity, or breach-password checking | Easy to crack offline or guess online |
| **Logic flaws in auth flow** | Bugs in login, registration, or password reset that allow bypassing authentication | e.g. resetting another user's password by manipulating a token or email parameter |
| **Insecure session tokens** | Tokens are guessable, not rotated after login, or not invalidated on logout | Session hijacking or fixation attacks |
| **Insecure cookie handling** | Cookies missing `HttpOnly`, `Secure`, or `SameSite` flags | Tokens exposed to XSS, sent over HTTP, or used in CSRF attacks |
| **No MFA** | Single factor authentication only — one stolen credential is enough | Credential stuffing leads directly to account takeover |

### Prevention

- Implement rate limiting and account lockout on authentication endpoints
- Check passwords against known breach databases (e.g. HaveIBeenPwned API)
- Invalidate session tokens on logout and rotate them after authentication
- Set cookies with `HttpOnly`, `Secure`, and `SameSite=Strict` or `Lax`
- Return identical error messages for invalid username and invalid password — prevent enumeration
- Enforce MFA for all accounts, especially privileged ones
- Use secure, time-limited tokens for password reset flows — single-use and bound to the requesting user

---

## A09 — Logging and Alerting Failures 📋

**What it is:** Applications fail to record or alert on security-relevant events — leaving defenders blind to ongoing attacks, unable to reconstruct incidents, and unable to meet compliance obligations.

> **The IAAA link:** Accountability is the fourth pillar of IAAA. Without logging, there is no accountability — and without accountability, there is no way to detect, investigate, or attribute an attack.

### What Happens Without Adequate Logging

| Consequence | Impact |
|-------------|--------|
| Attacks go undetected | Attackers operate freely with no alerts triggered |
| Incident response is blind | No timeline or evidence to reconstruct what happened |
| Compliance failures | Regulations like GDPR, PCI-DSS, and ISO 27001 require audit trails |
| Breach dwell time increases | Average breach dwell time is months — logging is how you catch it earlier |

### What Should Be Logged

| Event | Why |
|-------|-----|
| Failed login attempts | Detect brute force, credential stuffing, and password spraying |
| Successful logins (especially from new IPs/devices) | Detect account takeover |
| Access to sensitive data or resources | Detect unauthorised access and exfiltration |
| Privilege escalation or role changes | Detect insider threats and lateral movement |
| Configuration or permission changes | Detect persistence mechanisms and tampering |
| Input validation failures | Surface injection attempts and fuzzing |
| Errors and unhandled exceptions | Reveal exploitation attempts in progress |

### Logging vs Alerting

| Layer | Purpose | Tool Examples |
|-------|---------|--------------|
| **Logging** | Capture and store security events with sufficient detail | Application logs, syslog, CloudTrail |
| **Monitoring** | Aggregate and visualise log data | ELK Stack, Splunk, Graylog |
| **Alerting** | Trigger notifications when suspicious patterns are detected | SIEM rules, detection engineering, SOC workflows |

> **Logs that are never reviewed are security theatre.** Effective accountability requires all three layers — logs collected, centralised into a SIEM, and monitored with tuned detection rules. A SOC analyst's job is built on this chain.
