# Vulnerability Scanners

## 🔎 Overview

A **vulnerability scanner** automatically inspects systems, networks, and applications to identify weaknesses — misconfigurations, outdated software, missing patches, default credentials, and known CVEs. Results are compiled into detailed reports that help prioritise remediation.

> Vulnerability scanning is a core part of **vulnerability management** — a continuous cycle of discovering, assessing, prioritising, and remediating weaknesses before attackers can exploit them.

---

## 🔐 Authenticated vs Unauthenticated Scans

| Type | Credentials Required | Perspective | What It Finds | Best For |
|------|---------------------|-------------|---------------|----------|
| **Authenticated** | ✅ Yes | Internal — as a logged-in user | Misconfigs, installed software vulnerabilities, patch gaps, weak permissions | Deep host audits, compliance checks |
| **Unauthenticated** | ❌ No (IP only) | External — as an outsider | Exposed services, open ports, publicly exploitable vulnerabilities | Simulating external attacker view |

---

## 🌐 Internal vs External Scans

| Type | Where It Runs | Simulates | Focus |
|------|--------------|-----------|-------|
| **Internal** | Inside the network (behind the firewall) | Insider threat or compromised internal host | Lateral movement paths, internal misconfigs |
| **External** | Outside the network (internet-facing) | External attacker | Attack surface, exposed services, perimeter weaknesses |

---

## 🛠️ Tools

| Tool | Licence | Deployment | Key Features |
|------|---------|------------|--------------|
| **Nessus** | Free (limited) / Commercial | On-prem or cloud | Most widely used in enterprise — commercial adds unlimited scans, advanced plugins, and support |
| **Qualys** | Subscription | Cloud-based | Continuous scanning, compliance checks (PCI, CIS), asset management |
| **Nexpose** | Subscription | Cloud or on-prem | Auto-discovers new assets, risk-scores vulnerabilities by asset value and impact, compliance reporting |
| **OpenVAS** | Open source | On-prem | Community-maintained, basic feature set — ideal for small orgs, home labs, or individuals |

> All scanners generate detailed reports after every scan — output typically includes vulnerability descriptions, CVSS scores, affected hosts, and remediation guidance.

---

## 🏷️ CVE & CVSS

### CVE — Common Vulnerabilities and Exposures

A **CVE** is a unique, standardised identifier assigned to every publicly known vulnerability.

- Maintained by the **MITRE Corporation**, sponsored by the US government
- Published in the **National Vulnerability Database (NVD)** at nvd.nist.gov
- Format: `CVE-<year>-<arbitrary digits>` — e.g. `CVE-2024-9374`
- Each CVE entry includes a description, affected products, and links to patches or advisories

### CVSS — Common Vulnerability Scoring System

**CVSS** provides a standardised numerical score representing the severity of a vulnerability. Scores are calculated based on factors like attack vector, complexity, required privileges, and impact on confidentiality, integrity, and availability.

| Score | Severity | Typical Response |
|-------|----------|-----------------|
| 0.0 | None | Informational only |
| 0.1 – 3.9 | 🟢 Low | Patch in next scheduled cycle |
| 4.0 – 6.9 | 🟡 Medium | Patch within weeks |
| 7.0 – 8.9 | 🟠 High | Patch urgently — within days |
| 9.0 – 10.0 | 🔴 Critical | Patch immediately — active exploitation likely |

> Currently on **CVSS v3.1** (v4.0 released in 2023). Scores can vary slightly between sources depending on the version used.

---

## 🟢 OpenVAS

**OpenVAS** (Open Vulnerability Assessment System) is the open source vulnerability scanner maintained by Greenbone Networks. It is available as a standalone install or as a Docker image.

### Installation (via Docker)

Using Docker is the easiest way to get OpenVAS running without complex dependency management:

```bash
# Step 1 — Install Docker
sudo apt install docker.io

# Step 2 — Pull and run the OpenVAS container (first run downloads the image)
sudo docker run -d -p 443:443 --name openvas immauss/openvas

# Step 3 — Start the container (if already installed but stopped)
docker start openvas
```

> ⚠️ The first run may take several minutes as it downloads and sets up the vulnerability feed database.

### Access

Once running, open any browser and navigate to:
```
https://127.0.0.1
```
Accept the self-signed certificate warning — this is expected. The dashboard shows an overview of all scans, tasks, and results.

---

### 📋 Performing a Scan

```
1. Create a task     →  Tasks → Scans → ⭐ (New Task icon) → enter a task name
2. Set the target    →  Scan Targets → enter the target machine name and IP address
3. Configure         →  Select scan options (scan type, policy) → Create
4. Start the scan    →  Task dashboard → ▶ play button in the Actions column
5. View details      →  Click the task name to see scan progress and metadata
6. View results      →  Click the vulnerability count under the target host to see the full findings list
```

> **Scan policies** control the depth and type of checks performed — a full scan is thorough but slow; a discovery scan is fast but shallow. Choose based on your goal and available time.
