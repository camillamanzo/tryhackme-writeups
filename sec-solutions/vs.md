# Vulnerability Scanners

## Overview

A **vulnerability scanner** inspects systems to find weaknesses — misconfigurations, outdated software, known CVEs, and more.

---

## Scan Types

### By Authentication

| Type | Credentials Required | What it finds | Notes |
|------|---------------------|---------------|-------|
| **Authenticated** | Yes | Vulnerabilities exploitable with host access | Deeper visibility — scans configs and installed apps |
| **Unauthenticated** | No (IP only) | Vulnerabilities exploitable by external attackers | Less resource-intensive, easier to set up |

### By Network Position

| Type | Where it runs | Focus |
|------|--------------|-------|
| **Internal** | Inside the network | Vulnerabilities exploitable from within |
| **External** | Outside the network | Vulnerabilities exploitable from outside |

---

## Tools

All scanners generate detailed reports after every scan.

| Tool | Model | Key Features |
|------|-------|--------------|
| **Nessus** | Free (limited) / Commercial | Widely used in enterprises; commercial version adds advanced scanning, unlimited scans, and support |
| **Qualys** | Subscription / Cloud | Continuous scanning, compliance checks, asset management |
| **Nexpose** | Subscription / Cloud or on-prem | Auto-discovers new assets, scores vulnerabilities by asset value and impact, compliance checks |
| **OpenVAS** | Open source | Basic features; good for small orgs or individual use |

---

## CVE & CVSS

### CVE — Common Vulnerabilities and Exposures

- Unique identifier assigned to every known vulnerability
- Maintained by the **MITRE Corporation**
- Format: `CVE-<year>-<arbitrary digits>` — e.g. `CVE-2024-9374`

### CVSS — Common Vulnerability Scoring System

Rates the severity of a vulnerability on a 0–10 scale:

| Score | Severity |
|-------|----------|
| 0.0 – 3.9 | Low |
| 4.0 – 6.9 | Medium |
| 7.0 – 8.9 | High |
| 9.0 – 10.0 | Critical |

---

## OpenVAS

Open source vulnerability assessment solution.

### Installation (via Docker)

```bash
# Install Docker
sudo apt install docker.io

# Pull and run the OpenVAS container
sudo docker run -d -p 443:443 --name openvas immauss/openvas

# Start the container (if stopped)
docker start openvas
```

### Access

Navigate to `https://127.0.0.1` in any browser. A dashboard will load showing an overview of all vulnerability scans.

### Performing a Scan

1. **Create a task:** Tasks → Scans → ⭐ (star icon) → New Task → enter task name
2. **Set the target:** Click *Scan Targets* → enter the target machine name (e.g. `windows-machine`) and its IP address
3. **Configure and save:** Add scan options → click *Create*
4. **Start the scan:** Go to the task dashboard → click the ▶ play button in the *Actions* column
5. **View scan details:** Click on the task name
6. **View vulnerabilities:** Click on the vulnerability count under the target host in the *Results* section
