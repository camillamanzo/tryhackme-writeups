# Incident Response Fundamentals

## 🚨 Overview

Devices and processes constantly generate **events** — logs of everything they do. Security solutions ingest these events and flag suspicious activity as **alerts**. The SOC team then analyses these alerts to determine whether they represent real threats.

### Alerts: True vs False Positives

| Type | Definition | Example |
|------|------------|---------|
| **False Positive** | Alert points to something that *looks* dangerous but is actually harmless | High data transfer flagged as exfiltration — turns out it was a scheduled cloud backup |
| **True Positive** | Alert points to something genuinely dangerous — this becomes an **incident** | Phishing email detected, confirmed to have targeted a user to compromise their system |

### Incident Severity Levels

True positive alerts are classified as **incidents** and assigned a severity level to prioritise response.

| Severity | Priority |
|----------|----------|
| 🔴 **Critical** | Highest — respond immediately |
| 🟠 **High** | Second priority |
| 🟡 **Medium** | Third priority |
| 🟢 **Low** | Lowest priority |

---

## ⚠️ Incident Types

| Type | Description |
|------|-------------|
| **Malware Infection** | Malicious program that damages systems — mostly delivered via files |
| **Security Breach** | An unauthorised person gains access to confidential data |
| **Data Leak** | Confidential information is exposed to unauthorised entities |
| **Insider Attack** | Malicious or negligent incident originating from within the organisation |
| **Denial of Service (DoS)** | Systems, networks or applications are made unavailable by flooding with false requests |

---

## 🔁 Incident Response Processes

### SANS Framework (6 Steps)

| Step | Phase | Description |
|------|-------|-------------|
| 1 | **Preparation** | Build the resources to handle incidents — plans, teams, security solutions |
| 2 | **Identification** | Monitor events with security solutions and look for abnormal behaviour |
| 3 | **Containment** | Minimise the impact — isolate the victim machine or disable compromised accounts |
| 4 | **Eradication** | Remove the threat — ensure the environment is clean |
| 5 | **Recovery** | Restore affected systems from backup or rebuild — test before returning to production |
| 6 | **Lessons Learned** | Document gaps in detection and analysis — improve processes for the future |

### NIST Framework (4 Phases)

| Phase | Description |
|-------|-------------|
| **Preparation** | Establish resources, policies and plans |
| **Detection & Analysis** | Identify and investigate incidents |
| **Containment, Eradication & Recovery** | Limit impact, remove threat, restore operations |
| **Post-Incident Activity** | Review and improve |

### SANS vs NIST at a Glance

```
SANS:  Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned
                                          ↕ maps to ↕
NIST:  Preparation → Detection & Analysis → Containment/Eradication/Recovery → Post-Incident
```

> SANS breaks the process into 6 granular steps — better for operational teams. NIST uses 4 broader phases — better for policy and governance frameworks.

---

## 🛠️ Incident Response Techniques & Tools

| Tool/Method | Description |
|-------------|-------------|
| **SIEM** | Security Information & Event Management — collects logs from across the environment into a centralised location and correlates them to identify incidents |
| **AV (Antivirus)** | Detects known malicious programs — regularly scans systems for threats |
| **EDR** | Endpoint Detection & Response — deployed on every endpoint, protects against advanced threats and can automatically eradicate them |
| **Playbooks** | Step-by-step instructions for handling each type of incident — guides analysts through the correct response |
| **Runbooks** | Detailed step-by-step execution guides for specific actions taken during different incident types |

### Playbooks vs Runbooks

| Document | Purpose |
|----------|---------|
| **Playbook** | High-level guide — *what* to do for each incident type |
| **Runbook** | Detailed execution steps — *how* to do each specific action |
