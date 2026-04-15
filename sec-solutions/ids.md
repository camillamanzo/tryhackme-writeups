# IDS — Intrusion Detection Systems

## Overview

An **Intrusion Detection System (IDS)** monitors network traffic and generates alerts — it does not take action to block or stop threats.

---

## Types

| Type | Description |
|------|-------------|
| **HIDS** (Host IDS) | Installed on individual hosts; monitors only that host |
| **NIDS** (Network IDS) | Monitors traffic across all hosts; provides a centralised detection view |

---

## Detection Modes

| Mode | How it works | Limitation |
|------|--------------|------------|
| **Signature-based** | Matches traffic against a database of known attack patterns | Cannot detect zero-days |
| **Anomaly-based** | Learns a baseline of normal behaviour, then flags deviations | May produce false positives |
| **Hybrid** | Combines both signature and anomaly methods to leverage their strengths | — |

---

## Snort

Open source, **hybrid IDS** (signature-based + anomaly-based).

### Modes

#### Packet Sniffer Mode
- Reads and displays network packets without analysing them
- Useful for network monitoring and troubleshooting
- **Use when:** detailed insight into traffic is needed to diagnose network performance issues

#### Packet Logging Mode
- Performs detection on network traffic in real-time
- Detections displayed as alerts on the console or written to a `.pcap` file
- **Use when:** traffic logs are needed for root cause analysis

#### NIDS Mode
- Monitors network traffic in real-time and applies rule files to identify matches
- Signature matches generate alerts
- **Use when:** proactively monitoring network or systems to detect potential threats

---

### Configuration

| Path | Purpose |
|------|---------|
| `/etc/snort/snort.conf` | Main config file — defines enabled rules, network range to monitor, etc. |
| `/etc/snort/rules/` | Directory containing rule files |

**Enable promiscuous mode** on the host's network interface so Snort can capture traffic across the whole network, not just traffic addressed to that host.

---

### Rule Syntax

```
action protocol sourceIP sourcePort -> destinationIP destinationPort (ruleMetadata)
```

| Field | Description |
|-------|-------------|
| `action` | What to do when the rule triggers (e.g. `alert`, `log`, `drop`) |
| `protocol` | Protocol to match (e.g. `tcp`, `udp`, `icmp`) |
| `sourceIP` | Source IP address of the traffic |
| `sourcePort` | Source port of the traffic |
| `destinationIP` | Destination IP — can be a variable defined in `snort.conf` (e.g. `$HOME_NET`) |
| `destinationPort` | Destination port |
| `msg` | Human-readable description of the alert |
| `sid` | Signature ID — unique rule identifier |
| `rev` | Rule revision number |

**Example:**
```
alert icmp any any -> $HOME_NET any (msg:"ping detected"; sid:10001; rev:1;)
```

---

### Rule Creation

Add a new rule to the local rules file:

```bash
sudo nano /etc/snort/rules/local.rules
```

Example rule:
```
alert icmp any any -> 127.0.0.1 any (msg:"Loopback Ping Detected"; sid:10003; rev:1;)
```

---

### Rule Testing (Live Interface)

**Step 1 — Start Snort:**
```bash
sudo snort -q -l /var/log/snort -i lo -A console -c /etc/snort/snort.conf
```

| Flag | Description |
|------|-------------|
| `-q` | Quiet mode — suppresses banner, init messages, and packet count summaries |
| `-l /var/log/snort` | Sets the log output directory |
| `-i lo` | Specifies the network interface to listen on (`lo` = loopback) |
| `-A console` | Prints alerts directly to the terminal |
| `-c /etc/snort/snort.conf` | Points to the config file so Snort knows which rules to load |

**Step 2 — Trigger the rule:**
```bash
ping 127.0.0.1
```

**Step 3 — Verify** that Snort generated a `Loopback Ping Detected` alert in the terminal.

---

### Running Snort on PCAP Files

To perform detections on historical network traffic saved in a `.pcap` file:

```bash
sudo snort -q -l /var/log/snort -r path/to/file.pcap -A console -c /etc/snort/snort.conf
```

| Flag | Description |
|------|-------------|
| `-q` | Quiet mode |
| `-l /var/log/snort` | Log output directory |
| `-r path/to/file.pcap` | Tells Snort to read from a saved capture file instead of a live interface |
| `-A console` | Prints alerts to the terminal |
| `-c /etc/snort/snort.conf` | Config file with rules to apply |
