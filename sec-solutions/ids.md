# IDS — Intrusion Detection Systems

## 🔍 Overview

An **Intrusion Detection System (IDS)** monitors network traffic and generates alerts — it does **not** take action to block or stop threats. It is a passive security control, unlike an IPS (Intrusion Prevention System) which actively blocks traffic.

> **IDS vs IPS:** An IDS detects and alerts. An IPS detects, alerts, *and* blocks. Many modern tools (including Snort) can operate in both modes.

---

## 🖥️ IDS Types

| Type | Scope | Where It Runs | Description |
|------|-------|--------------|-------------|
| **HIDS** (Host IDS) | Single host | On the host itself | Monitors logs, file system changes, and process activity on that machine only |
| **NIDS** (Network IDS) | Whole network | On a network tap or mirrored port | Monitors all traffic flowing through the network — centralised detection view |

---

## 🧠 Detection Modes

| Mode | How It Works | Strength | Limitation |
|------|--------------|----------|------------|
| **Signature-based** | Matches traffic against a database of known attack patterns (like antivirus) | Accurate for known threats, low false positives | Cannot detect zero-days or novel attacks |
| **Anomaly-based** | Learns a baseline of normal behaviour, then flags deviations | Can detect unknown threats | Higher false positive rate — legitimate unusual behaviour may trigger alerts |
| **Hybrid** | Combines signature and anomaly detection | Best of both — broader coverage | More complex to tune and maintain |

---

## 🐷 Snort

**Snort** is an open source, hybrid IDS/IPS widely used in both enterprise and lab environments. It supports signature-based and anomaly-based detection and can run in three distinct modes.

### Modes

| Mode | What It Does | Output | Use When |
|------|--------------|--------|----------|
| **Packet Sniffer** | Reads and displays packets in real-time — no analysis | Console output | Diagnosing network performance issues or inspecting raw traffic |
| **Packet Logging** | Captures and logs traffic to disk — no active rule matching | `.pcap` files | Collecting traffic logs for later root cause analysis |
| **NIDS** | Monitors traffic in real-time and applies rule files — generates alerts on matches | Alerts to console or log | Proactive threat detection across a network or system |

---

### ⚙️ Configuration

| Path | Purpose |
|------|---------|
| `/etc/snort/snort.conf` | Main config file — defines enabled rules, variables (e.g. `$HOME_NET`), output plugins, and network range to monitor |
| `/etc/snort/rules/` | Directory containing rule files (e.g. `local.rules`, `community.rules`) |

> **Promiscuous mode:** Enable it on the host's network interface so Snort captures all traffic on the network segment — not just traffic addressed to that host. Without it, Snort only sees its own traffic.

---

### 📏 Rule Syntax

```
action protocol sourceIP sourcePort -> destinationIP destinationPort (msg:"..."; sid:XXXXX; rev:X;)
```

| Field | Description | Example Values |
|-------|-------------|----------------|
| `action` | What to do when the rule triggers | `alert`, `log`, `drop`, `reject` |
| `protocol` | Protocol to match | `tcp`, `udp`, `icmp`, `ip` |
| `sourceIP` | Source IP address | `any`, `192.168.1.0/24` |
| `sourcePort` | Source port | `any`, `80`, `1024:65535` |
| `->` | Direction of traffic flow | Always source → destination |
| `destinationIP` | Destination IP — can use variables from `snort.conf` | `$HOME_NET`, `10.0.0.1` |
| `destinationPort` | Destination port | `any`, `443`, `22` |
| `msg` | Human-readable alert description (shown in logs) | `"ping detected"` |
| `sid` | Signature ID — must be unique per rule | `10001` (custom rules start at 1000000+) |
| `rev` | Rule revision — increment when updating a rule | `1` |

**Example rule:**
```
alert icmp any any -> $HOME_NET any (msg:"ping detected"; sid:10001; rev:1;)
```

---

### ✏️ Rule Creation

Open the local rules file and add your custom rule:

```bash
sudo nano /etc/snort/rules/local.rules
```

Example — alert on loopback ping:
```
alert icmp any any -> 127.0.0.1 any (msg:"Loopback Ping Detected"; sid:10003; rev:1;)
```

> **SID ranges:** Snort reserves SIDs below 1,000,000 for official rules. Use SIDs ≥ 1,000,000 for local custom rules to avoid conflicts.

---

### 🧪 Rule Testing — Live Interface

**Step 1 — Start Snort on the loopback interface:**
```bash
sudo snort -q -l /var/log/snort -i lo -A console -c /etc/snort/snort.conf
```

| Flag | Value | Description |
|------|-------|-------------|
| `-q` | — | Quiet mode — suppresses banner, init messages, and packet count summaries |
| `-l` | `/var/log/snort` | Log output directory |
| `-i` | `lo` | Network interface to listen on (`lo` = loopback) |
| `-A` | `console` | Alert mode — prints alerts directly to the terminal |
| `-c` | `/etc/snort/snort.conf` | Config file — tells Snort which rules to load |

**Step 2 — Trigger the rule:**
```bash
ping 127.0.0.1
```

**Step 3 — Verify** that Snort generated a `Loopback Ping Detected` alert in the terminal.

---

### 📁 Running Snort on PCAP Files

Used to replay and analyse historical network traffic captures:

```bash
sudo snort -q -l /var/log/snort -r path/to/file.pcap -A console -c /etc/snort/snort.conf
```

| Flag | Value | Description |
|------|-------|-------------|
| `-q` | — | Quiet mode |
| `-l` | `/var/log/snort` | Log output directory |
| `-r` | `path/to/file.pcap` | Read from a saved capture file instead of a live interface |
| `-A` | `console` | Print alerts to the terminal |
| `-c` | `/etc/snort/snort.conf` | Config file with rules to apply |

> **Tip:** PCAP files can be captured with tools like `tcpdump` or Wireshark and then analysed offline with Snort — useful for forensic investigation or testing new rules against known traffic.
