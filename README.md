# 🔐 TryHackMe Write-Ups

Cybersecurity write-ups from TryHackMe labs covering offensive &amp; defensive security. I have summarised the topics I have studies so far, and will continue to do so for the whole Cybersecurity101 learning path.

> **About me:** CompTIA Security+ certified professional with 4+ years in full-stack development and IT/OT consulting, now deepening hands-on cybersecurity skills. Background in RBAC implementation, MES infrastructure, and secure application development.

[![TryHackMe](https://img.shields.io/badge/TryHackMe-camillamanzo-red?style=flat&logo=tryhackme)](https://tryhackme.com/p/CamiM98)
[![Security+](https://img.shields.io/badge/CompTIA-Security%2B-red?style=flat)](https://www.credly.com/badges/0ab21ebf-70c0-4396-bbf4-76f8c4489d30/public_url)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Camilla%20Manzo-blue?style=flat&logo=linkedin)](https://www.linkedin.com/in/camilla-manzo-a18a65228/)

---
 
## 📂 Write-Up Index

### 🐧 Linux & Systems
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Linux Fundamentals | Easy | processes, filesystem, shell op., ssh, dir | [📄 Read](linux/linux-fundamentals.md) |
 
### 🪟 Windows & Active Directory
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Windows Fundamentals | Easy | compmgmt, msinfo32, security, cmd, AD | [📄 Read](windows/windows-fundamentals.md) |
 
### 💻 Command Line
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Windows Command Line | Easy | CMD, PowerShell, scripting basics | [📄 Read](command-line/windows-cli.md) |
| Linux Command Line | Easy | Bash, file ops, piping, grep | [📄 Read](command-line/linux-cli.md) |
 
### 🌐 Networking
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Networking | Easy | OSI model, core protocols, secure protocols | [📄 Read](networking/networking-fundamentals.md) |
| Wireshark | Easy | traffic analyser | [📄 Read](networking/wireshark.md) |
| Tcpdump | Easy | command-line packet monitor | [📄 Read](networking/tcpdump.md) |
| Nmap | Easy | network scanner | [📄 Read](networking/nmap.md) |
 
### 🔑 Cryptography
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Cryptography | Easy | symmetric, PK cryptography, hashing | [📄 Read](crypto/cryptography.md) |
| John The Ripper | Easy | auth hashes, rules, zip/rar, ssh | [📄 Read](crypto/jtr.md) |
 
### ⚔️ Exploitation Basics
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Moniker Link (CVE-2024-21413) | Easy | vulnerabilities, payloads, shells | [📄 Read](offensive/moniker.md) |
| Metasploit | Easy | intro, exploitation, meterpreter | [📄 Read](offensive/metasploit.md) |
| Attack Example | Easy | recon, gain access, escalate, cracking, flag hunting | [📄 Read](offensive/attack.md) |
 
### 🌍 Web Application Hacking
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Web Appplication Basics | Easy | HTTP, cookies, headers, status codes | [📄 Read](web/web-app-basics.md) |
| JavaScript | Medium | JS overview | [📄 Read](web/js.md) |
| SQL | Medium | databases, SQL, commands | [📄 Read](web/sql.md) |
| Burp Suite | Easy | proxy, repeater, intruder | [📄 Read](web/burp-suite-basics.md) |

### 🌍 Offensive Security Tooling
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Hydra | Easy | commands, ssh, post form | [📄 Read](off-tooling/hydra.md) |
| GoBusters | Easy | commands, dir & file, dns, vhost | [📄 Read](off-tooling/gobusters.md) |
| Shells | Easy | reverse, bind, listener, payloads, web | [📄 Read](off-tooling/shells.md) |
| SQLMap | Easy | db, sql injection, injection tool | [📄 Read](off-tooling/sqlmap.md) |

### 🌍 Defensive Security 
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| Overview | Easy | soc, digital forensic, incident response | [📄 Read](def-tooling/overview.md) |
| SOC Fundamentals | Easy | baseline, detection and response, roles | [📄 Read](def-tooling/soc.md) |
| Digital Forensic Fundamentals | Easy | methodology, acquisition, windows forensics | [📄 Read](def-tooling/df.md) |
| incidents response Fundamentals | Easy | process, techniques | [📄 Read](def-tooling/ir.md) |
| Logs Fundamentals | Easy | event viewer, access.log analysis | [📄 Read](def-tooling/logs.md) |

### 🌍 Security Solutions
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| SIEM | Easy | features, log sources and ingestion, alert and analysis | [📄 Read](sec-solutions/siem.md) |
| Firewalls | Easy | types, rules, windows defender, linux iptables | [📄 Read](sec-solutions/fw.md) |
| IDS | Easy | overview, snort | [📄 Read](sec-solutions/ids.md) |
| Vulnerability scanner | Easy | overview, tools, cve & cvss, openVAS | [📄 Read](sec-solutions/vs.md) |

### 🌍 Defensive Security Tooling
| Room | Difficulty | Topics | Write-Up |
|------|-----------|--------|----------|
| CyberChef | Easy | overview, interfaces, categories | [📄 Read](defensive/cyberchef.md) |
| CAPA | Easy | overview, result analsys | [📄 Read](defensive/capa.md) |
| REMnux | Easy |  | [📄 Read](defensive/remnux.md) |
| FlareVM | Easy |  | [📄 Read](defensive/flarevm.md) |

### 🔜 Coming Soon
| Module | Status |
|--------|--------|
| Defensive Security Tooling | 🔄 In Progress |
| OWASP Top 10 | ⏳ Upcoming |

---

## 🛠️ Tools Used

![Nmap](https://img.shields.io/badge/-Nmap-black?style=flat)
![Wireshark](https://img.shields.io/badge/-Wireshark-blue?style=flat)
![Burp Suite](https://img.shields.io/badge/-Burp%20Suite-orange?style=flat)
![Metasploit](https://img.shields.io/badge/-Metasploit-blue?style=flat)
![GoBuster](https://img.shields.io/badge/-GoBuster-black?style=flat)
![SQLMap](https://img.shields.io/badge/-SQLMap-red?style=flat)
![Nessus](https://img.shields.io/badge/-Nessus-green?style=flat)

---

## 📝 Write-Up Format

Each write-up follows this structure:

```
## Room: [Name]
**Difficulty:** Easy / Medium / Hard
**Category:** Offensive / Defensive / Web / Crypto / etc.

### Objective
What the room is about and what I aimed to learn.

### Approach & Methodology
Step-by-step walkthrough of my process — tools used, commands run, reasoning.

### Key Findings / Flags
Redacted or summarised — no full spoilers.

### What I Learned
Takeaways, new techniques, and how they connect to real-world scenarios.

### Tools & Commands Used
Quick reference of commands and flags used in the room.
```

---

## 🎯 Learning Path Progress

- [x] Linux Fundamentals
- [x] Windows Fundamentals
- [x] Active Directory Fundamentals
- [x] Command Line
- [x] Networking
- [x] Cryptography
- [x] Explpoitation Basics
- [x] Web Application Hacking
- [x] Offensive Security Tooling
- [x] Defensive Security
- [x] Security Solutions
- [ ] Defensive Security Tooling
- [ ] OWASP Top 10

---

## 📬 Contact

📧 camillamanzo98@gmail.com
🔗 [LinkedIn](https://www.linkedin.com/in/camilla-manzo-a18a65228/))
💻 [GitHub](https://github.com/camillamanzo)

---

*Write-ups are my own analysis and methodology notes. No full flag spoilers — the goal is to document thinking, not shortcut others' learning.*
