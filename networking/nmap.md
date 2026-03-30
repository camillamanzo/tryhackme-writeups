# Nmap

## 🔭 Overview

Nmap is a **network scanner** used to discover live devices on a network and identify services running on them.

> **Always run as root or with `sudo`** — otherwise Nmap restricts certain scan types.
> **Note:** Nmap scans may trigger IDS/IPS alerts — control aggressiveness with `-T` flags.

### Target Specification

| Format | Description | Example |
|--------|-------------|---------|
| Single IP | Scan one host | `192.168.0.1` |
| IP range | Scan a range using `-` | `192.168.0.1-10` |
| Subnet | Scan a subnet using `/` | `192.168.0.1/24` (equivalent to `192.168.0.0–255`) |
| Hostname | Use a hostname as target | `example.thm` |

### Probe Types

| Flag | Description |
|------|-------------|
| `-PS[portlist]` | TCP SYN probe |
| `-PA[portlist]` | TCP ACK probe |
| `-PU[portlist]` | UDP probe |

---

## 🏠 Live Host Discovery

| Flag | Description | Example |
|------|-------------|---------|
| `-sn` | Discover live hosts without scanning services (smarter version of ping) | `nmap -sn 192.168.0.1/24` |
| `-sL` | List targets without scanning them | `nmap -sL 192.168.0.1/24` |

---

## 🔌 Service Scanning

| Flag | Description | Example |
|------|-------------|---------|
| `-sT` | TCP connect scan — full three-way handshake | `nmap -sT 192.168.0.1` |
| `-sS` | Stealth/SYN scan — sends only SYN, avoids completing handshake. Fewer logs, no connection established | `nmap -sS 192.168.0.1` |
| `-sU` | UDP scan — discovers UDP services e.g. DNS, DHCP, NTP, SNMP, VoIP | `nmap -sU 192.168.0.1` |
| `-F` | Fast mode — scans 100 most common ports instead of default 1000 | `nmap -F 192.168.0.1` |
| `-p[range]` | Specify port range | `-p10-1024` / `-p25` / `-p-` (all ports) |

---

## 🔎 Extracting More Info

| Flag | Description | Example |
|------|-------------|---------|
| `-O` | OS detection — educated guess about target OS | `nmap -sS -O 192.168.124.211` |
| `-sV` | Version detection — identifies services and versions on open ports | `nmap -sV 192.168.0.1` |
| `-A` | Aggressive — combines `-O` + `-sV` + traceroute | `nmap -A 192.168.0.1` |
| `-Pn` | Scan hosts that appear to be down | `nmap -Pn 192.168.0.1` |

---

## ⏱️ Time Management

### Timing Templates

| Flag | Level | Description |
|------|-------|-------------|
| `-T0` | Paranoid | Slowest — evades IDS |
| `-T1` | Sneaky | Very slow |
| `-T2` | Polite | Slowed to reduce bandwidth |
| `-T3` | Normal | Default |
| `-T4` | Aggressive | Faster, assumes reliable network |
| `-T5` | Insane | Fastest — may miss results |

### Fine-Grained Timing

| Flag | Description |
|------|-------------|
| `--min-parallelism <n>` | Minimum number of simultaneous TCP/UDP port probes per host group |
| `--max-parallelism <n>` | Maximum number of simultaneous TCP/UDP port probes per host group |
| `--min-rate <n>` | Minimum packet send rate (packets per second) |
| `--max-rate <n>` | Maximum packet send rate (packets per second) |
| `--host-timeout <time>` | Maximum time to wait for a target host to respond |

---

## 📤 Output Options

### Verbosity & Debugging

| Flag | Description |
|------|-------------|
| `-v` / `-vv` / `-vvv` / `-vvvv` | Increasing verbosity — real-time scan progress |
| `v` (during scan) | Press `v` after scan starts to increase verbosity live |
| `-d` to `-d9` | Debugging output — max level is `-d9` |

### Saving Output

| Flag | Format |
|------|--------|
| `-oN fileName` | Normal output |
| `-oX fileName` | XML output |
| `-oG fileName` | Grep-able output |
| `-oA fileName` | All major formats at once |

---

## 🛡️ Vulnerability Check

### Full Vulnerability Scan Workflow

```bash
# Step 1 — Run vulnerability scan and save results
nmap -sV -sC --script vuln -oN blue.nmap 10.114.181.71
```

| Flag | Purpose |
|------|---------|
| `-sV` | Detect service versions |
| `-sC` | Run default scripts — gather details like common misconfigurations |
| `--script vuln` | Check for known CVEs |
| `-oN blue.nmap` | Save results to `blue.nmap` |

```bash
# Step 2 — Open results interactively (better than cat for long files)
less blue.nmap
# Search with /word | quit with q
```

---

## 📋 Quick Reference — Common Scan Combos

```bash
# Discover live hosts on a subnet
nmap -sn 192.168.0.1/24

# Stealth scan with OS and version detection
nmap -sS -O -sV 192.168.124.211

# Fast scan of 100 most common ports
nmap -F 192.168.0.1

# Scan all ports
nmap -p- 192.168.0.1

# Full aggressive scan with output saved
nmap -A -oN results.nmap 192.168.0.1

# Vulnerability scan
nmap -sV -sC --script vuln -oN vuln_scan.nmap 192.168.0.1
```
