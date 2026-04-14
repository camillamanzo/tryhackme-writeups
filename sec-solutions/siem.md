# SIEM

## 🔭 Overview

A **SIEM (Security Information and Event Management)** system collects logs from across an environment in a centralised location and correlates them to identify incidents.

### Log Source Types

| Type | Description | Examples |
|------|-------------|---------|
| **Host-Centric** | Events that occurred within or related to a specific host | File access, authentication attempts, process execution, registry changes, PowerShell execution |
| **Network-Centric** | Events involving communication between hosts or the internet | SSH connections, FTP file access, web traffic, VPN access, network file sharing |

### Core Features

| Feature | Description |
|---------|-------------|
| **Centralised Log Collection** | Collects logs from all sources via agents or APIs |
| **Log Normalisation** | Breaks logs into consistent fields — standardises format across different sources |
| **Log Correlation** | Finds relationships between logs to identify malicious patterns |
| **Real-Time Alerting** | Detects malicious activity based on detection rules and triggers alerts |
| **Dashboards & Reporting** | Presents normalised, ingested data for analysis and visualisation |

---

## 📂 Log Sources

### Windows

| Source | Description |
|--------|-------------|
| **Event Viewer** | Records every system event — assigns a unique Event ID to each activity type |

### Linux

| Log Path | Contents |
|----------|----------|
| `/var/log/httpd` | HTTP requests, responses and error logs |
| `/var/log/cron` | Cron job execution events |
| `/var/log/auth.log` / `/var/log/secure` | Authentication-related logs |
| `/var/log/kern` | Kernel-related events |

### Web Server

| Log Path | Contents |
|----------|----------|
| `/var/log/apache` or `/var/log/httpd` | Apache web server logs on Linux |

---

## 📥 Log Ingestion Methods

| Method | Description |
|--------|-------------|
| **Agent / Forwarder** | Lightweight agent installed on endpoints — configured to capture and forward important logs to the SIEM |
| **Syslog** | Protocol used to collect data from web servers, databases, network devices, etc. and send it in real time |
| **Manual Upload** | Users can upload offline log data for ad hoc analysis |
| **Port Forwarding** | SIEM listens on a specific port — endpoints are configured to forward logs to that port |

---

## 🔔 Alerting & Detection Rules

SIEM detection rules correlate log sources and trigger alerts when conditions are met.

### Example Rules

**Rule 1 — Event Log Cleared:**
```
IF  log_source = WinEventLog
AND EventID = 104
THEN trigger alert: "Event Log Cleared"
```
> Event ID `104` fires when a user clears or removes Windows event logs — a common attacker anti-forensics technique.

---

**Rule 2 — WHOAMI Execution Detected:**
```
IF  log_source = WinEventLog
AND EventCode = 4688
AND NewProcessName contains "whoami"
THEN trigger alert: "WHOAMI command execution detected"
```
> `whoami` is commonly run during the exploitation or privilege escalation phase to confirm which user context an attacker is operating in.

---

## 🔍 Alert Investigation Process

```
Alert Triggered
      ↓
Analyse alert → True Positive or False Positive?
      ↓                         ↓
True Positive             False Positive
      ↓                         ↓
Investigate further      Tune the rule to avoid similar cases
      ↓
Contact asset owner — inquire about the activity
      ↓
Suspicious activity confirmed?
      ↓
Isolate the infected host
Block the suspicious IP
Escalate as required
```

### Investigation Steps

| Step | Action |
|------|--------|
| **Triage** | Determine if the alert is a true or false positive |
| **False positive** | Tune the detection rule to avoid similar noise in future |
| **True positive** | Proceed with investigation |
| **Asset owner contact** | Reach out to the owner of the affected system to verify the activity |
| **Host isolation** | Isolate the infected host if suspicious activity is confirmed |
| **IP block** | Block the suspicious IP address |
