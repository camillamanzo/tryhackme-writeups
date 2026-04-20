# Security Principles

## 🔺 The Security Triads & Core Principles

### CIA Triad

The foundational model of information security — every security control ultimately maps back to one or more of these three properties.

| Principle | Description | Enforced By |
|-----------|-------------|-------------|
| **Confidentiality** | Only intended persons can access the data | Encryption, access controls, classification |
| **Integrity** | Data cannot be altered without detection | Hashing, digital signatures, checksums |
| **Availability** | Systems and services are accessible when needed | Redundancy, backups, failover, DDoS protection |

---

### CIANA — CIA + Non-Repudiation + Authenticity

An extension of the CIA triad adding two properties focused on trust and accountability.

| Principle | Description | Enforced By |
|-----------|-------------|-------------|
| **Non-Repudiation** | The original source cannot deny being the source of data or an action | Digital signatures, audit logs |
| **Authenticity** | Data genuinely originates from the claimed source | Certificates, signatures, MFA |

---

### Parkerian Hexad — CIA + Authenticity + Utility + Possession

A further extension of CIA adding three additional properties.

| Principle | Description |
|-----------|-------------|
| **Authenticity** | Data is from the claimed source |
| **Utility** | Information is in a usable and meaningful format |
| **Possession** | Information is protected from unauthorised access, theft, or control — even if confidentiality is intact |

> **Utility example:** Encrypted data you've lost the key to is still confidential — but its utility is zero. These are distinct properties.

---

### DAD Triad — The Attacker's Perspective

The inverse of CIA — what an attacker is trying to achieve.

| Attacker Goal | Breaks | Description |
|---------------|--------|-------------|
| **Disclosure** | Confidentiality | Unauthorised access to data |
| **Alteration** | Integrity | Unauthorised modification of data |
| **Destruction / Denial** | Availability | Taking systems offline or destroying data |

---

### 🛡️ Defence in Depth

A security strategy based on **multiple overlapping layers of controls** — so that if one layer fails, others remain. No single control is relied upon to protect an asset.

> Also called **multi-layer security** or **layered security**. Borrowed from military strategy: slow an attacker down across multiple barriers rather than relying on one strong perimeter.

**Examples of layers:** perimeter firewall → network segmentation → host-based firewall → EDR → application controls → encryption → logging and monitoring.

---

## 🏛️ Security Models

Formal models used to define and enforce security policies in systems.

---

### Bell-LaPadula Model — Confidentiality

Designed for **military-style classified systems**. Subjects and objects are assigned security levels (e.g. Unclassified → Secret → Top Secret).

| Property | Rule | Meaning |
|----------|------|---------|
| **Simple Security Property** | No read up | A subject at a lower security level **cannot read** an object at a higher level |
| **Star (★) Security Property** | No write down | A subject at a higher security level **cannot write** to an object at a lower level |
| **Discretionary Security Property** | Access matrix | Read/write permissions are controlled via an access matrix |

> **Goal:** Prevent classified data from leaking downward to lower-clearance users.

---

### Biba Model — Integrity

The complement to Bell-LaPadula — focused on **data integrity** rather than confidentiality. Integrity levels replace security levels.

| Property | Rule | Meaning |
|----------|------|---------|
| **Simple Integrity Property** | No read down | A higher-integrity subject **cannot read** from a lower-integrity object |
| **Star (★) Integrity Property** | No write up | A lower-integrity subject **cannot write** to a higher-integrity object |

> **Goal:** Prevent untrusted (low-integrity) data from corrupting trusted (high-integrity) data.

---

### Clark-Wilson Model — Integrity

A more practical integrity model designed for **commercial environments** (e.g. accounting systems). It enforces integrity through well-defined transactions rather than just access levels.

| Component | Abbreviation | Description |
|-----------|-------------|-------------|
| **Constrained Data Item** | CDI | Data whose integrity must be preserved (e.g. financial records) |
| **Unconstrained Data Item** | UDI | Data outside the integrity boundary (e.g. raw user input) |
| **Transformation Procedures** | TPs | The only operations allowed to modify CDIs (read, write, update) |
| **Integrity Verification Procedures** | IVPs | Procedures that check and validate the correctness and consistency of CDIs |

> **Key idea:** Users never interact with CDIs directly — they can only use approved TPs, which act as controlled transactions. This mirrors double-entry bookkeeping.

---

## 📐 ISO/IEC 19249

**ISO** (International Organisation for Standardisation) / **IEC** (International Electrotechnical Commission) standard defining architectural and design principles for secure systems.

---

### Architectural Principles

| # | Principle | Description |
|---|-----------|-------------|
| 1 | **Domain Separation** | Components (apps, data, resources) are grouped into domains with defined security attributes. CPU rings are an example — Ring 0 is most privileged (kernel), Ring 3 is least privileged (user space) |
| 2 | **Layering** | Layered structure (like the OSI model) allows different security policies to be applied at each level |
| 3 | **Encapsulation** | Low-level implementations are hidden from higher layers to prevent direct manipulation — e.g. OOP encapsulation, abstraction in APIs |
| 4 | **Redundancy** | Ensures availability and integrity through duplication — e.g. RAID 5 across 3 drives tolerates one drive failure without data loss |
| 5 | **Virtualisation** | A single set of hardware is shared among multiple OS instances — enables isolation, sandboxing, and resource efficiency |

---

### Design Principles

| # | Principle | Description |
|---|-----------|-------------|
| 1 | **Least Privilege** | Grant only the minimum permissions needed to perform a task — limits blast radius if an account is compromised |
| 2 | **Attack Surface Minimisation** | Reduce exposure by hardening systems — disable unused services, close open ports, remove unnecessary software |
| 3 | **Centralised Parameter Validation** | Validate all input in a single, trusted library or system — avoids inconsistent validation leading to injection or bypass |
| 4 | **Centralised General Security Services** | Centralise authentication, logging, and other security services — but architect to avoid a single point of failure |
| 5 | **Prepare for Error and Exception Handling** | Design systems to **fail safe** — on error, default to a secure state (fail closed), not an open/permissive one |

---

## 🔒 Zero Trust

**"Never trust, always verify"** — a security model that eliminates implicit trust based on network location or ownership.

| Principle | Description |
|-----------|-------------|
| **No implicit trust** | Being inside the corporate network does not grant access — every request is treated as potentially hostile |
| **Always authenticate** | Authentication and authorisation are required before accessing any resource, regardless of origin |
| **Least privilege access** | Users and systems receive only the access they need for the current task |
| **Assume breach** | Design systems assuming an attacker is already inside — minimise lateral movement |

**Micro-segmentation** is a key implementation technique:
- The network is divided into segments that can be as small as a single host
- Communication between segments requires explicit authentication and ACL checks
- Limits an attacker's ability to move laterally even after gaining a foothold

> Zero trust is increasingly the standard in cloud and hybrid environments where the traditional network perimeter no longer exists.

---

## ⚠️ Vulnerability, Threat & Risk

Three distinct but related concepts — often confused.

| Term | Definition | Example |
|------|-----------|---------|
| **Vulnerability** | A weakness in a system that could be exploited | Unpatched RCE vulnerability in a public-facing web server |
| **Threat** | A potential danger associated with a vulnerability — a threat actor or event that could exploit it | A ransomware group known to exploit that vulnerability |
| **Risk** | The likelihood that a threat actor exploits a vulnerability, multiplied by the impact on the business | High likelihood + critical system = high risk |

> **Risk formula (simplified):** `Risk = Likelihood × Impact`
>
> A vulnerability with no known threat actor and low impact = low risk. The same vulnerability in a critical system targeted by active threat groups = critical risk. Context determines prioritisation.
