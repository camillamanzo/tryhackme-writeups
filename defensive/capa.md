# CAPA — Common Analysis Platform of Artifacts

## 🔍 Overview

**CAPA** is an open source tool developed by Mandiant that automatically identifies capabilities in executable programs. It encodes reverse engineering knowledge into an automated analysis framework — instead of manually disassembling a binary, CAPA applies thousands of rules to tell you what a program *can do*.

- Useful in **malware analysis**, **threat hunting**, and **incident response**
- Works without executing the binary — purely static analysis
- Output maps to industry-standard frameworks: MITRE ATT&CK, MBC, and MAEC
- **CAPA Web Explorer:** upload CAPA-generated JSON files for interactive browser-based analysis → https://mandiant.github.io/capa/explorer/#/

---

## 📂 Supported File Types

| Format | Description |
|--------|-------------|
| **PE** (Portable Executable) | Standard Windows `.exe` and `.dll` binaries |
| **ELF** | Linux executables |
| **.NET modules** | Managed code assemblies |
| **Shellcode** | Raw position-independent code |
| **Sandbox reports** | Output from dynamic analysis sandboxes (e.g. Cape, Drakvuf) |

---

## ⚙️ How It Works

```
1. Analyses the file — inspects instructions, strings, imports, and structure
2. Applies rule files — each rule describes a known behaviour or capability pattern
3. Reports matches — lists what the program is capable of doing, mapped to ATT&CK / MBC
```

> Rules are written in YAML and maintained by the community at github.com/mandiant/capa-rules. Each rule targets a specific behaviour (e.g. "encrypts data using AES", "enumerates running processes").

---

## 🖥️ Running CAPA

### Setup

```powershell
# Check current directory
pwd

# Navigate to CAPA directory if needed
cd C:\Users\Administrator\Desktop\capa
```

### Core Commands

| Command | Description |
|---------|-------------|
| `capa.exe .\target.bin` | Basic analysis — outputs capability matches to the terminal |
| `capa.exe .\target.bin -v > output.txt` | Verbose output — includes matched rule details, saved to file |
| `capa.exe -vv .\target.bin > output.txt` | Very verbose — shows *why* each rule triggered (matched bytes, strings, logic) |
| `capa.exe -j -vv .\target.bin > output.json` | JSON output — ideal for structured analysis or uploading to CAPA Web Explorer |
| `Get-Content .\output.txt` | Read a generated output file in PowerShell |
| `capa -h` | Show all available flags and options |

### Output Formats

| Flag | Format | Best For |
|------|--------|----------|
| *(none)* | Plain text table | Quick terminal review |
| `-v` | Verbose text | Detailed rule matches |
| `-vv` | Very verbose text | Understanding *why* a rule fired |
| `-j` | JSON | Programmatic analysis, CAPA Web Explorer |

> **Tip:** Combine `-j` and `-vv` together to get the richest JSON output for Web Explorer upload:
> ```powershell
> capa.exe -j -vv .\cryptbot.bin > cryptbot_vv.json
> ```

---

## 📊 Analysing Results

### First Block — File Metadata

The top section of every CAPA result describes how the analysis was performed:

| Field | Description |
|-------|-------------|
| **md5 / sha256** | Hash of the analysed file — use to look it up on VirusTotal |
| **analysis** | How CAPA analysed the file (e.g. static, dynamic) |
| **os** | Target operating system of the binary (e.g. `windows`, `linux`) |
| **arch** | CPU architecture — e.g. `x86`, `x86_64`, `amd64` |
| **path** | Full path to the analysed file on disk |
| **Cryptographic algorithm** | If detected, the crypto scheme used by the binary |

---

## 🗺️ Framework Mappings

CAPA maps its findings to three industry-standard frameworks:

---

### MITRE ATT&CK

A globally recognised repository documenting the tactics, techniques, and procedures (TTPs) used by real-world threat actors.

**Formats:**

```
ATT&CK Tactic :: ATT&CK Technique :: Technique Identifier
ATT&CK Tactic :: ATT&CK Technique :: Sub-Technique :: Identifier.Sub-Identifier
```

**Example:**
```
Defense Evasion :: Virtualization/Sandbox Evasion :: T1497.001
```

| Component | Description |
|-----------|-------------|
| **Tactic** | The adversary's high-level goal (e.g. Persistence, Exfiltration) |
| **Technique** | How they achieve that goal (e.g. Registry Run Keys) |
| **Sub-Technique** | A more specific variant of the technique |
| **Identifier** | Unique numeric ID (e.g. `T1497`, `T1497.001`) |

---

### MAEC — Malware Attribute Enumeration and Characterisation

A structured language for describing malware characteristics and behaviour in a standardised way.

| Field | Description | Example |
|-------|-------------|---------|
| **maec/category** | What category of malware this is | `ransomware`, `trojan`, `dropper` |
| **maec/value** | Specific role or behaviour of the sample | `launcher`, `downloader`, `backdoor` |

---

### MBC — Malware Behaviour Catalogue

A malware-specific extension of ATT&CK, focused on low-level malware behaviours and micro-behaviours.

**Formats:**
```
OBJECTIVE :: Behavior :: Method [Identifier]
OBJECTIVE :: Behavior :: [Identifier]
```

| Component | Description |
|-----------|-------------|
| **Objective** | High-level goal — based on ATT&CK tactics but framed in malware context |
| **Behavior** | A specific action the malware performs (may include micro-behaviours) |
| **Method** | A particular implementation of that behaviour |
| **Identifier** | Unique MBC ID |

**Example:**
```
Anti-Behavioral Analysis :: Virtual Machine Detection :: Check Registry [B0009.005]
```

---

## 🗂️ Namespaces

CAPA uses **namespaces** to organise rules by purpose. Every rule belongs to a namespace hierarchy.

**Format:**
```
Capability (Rule Name) :: Top-Level Namespace / Namespace
```

**Example namespace tree:**
```
anti-analysis/
├── anti-vm/
│   ├── vm-detection/
│   │   ├── reference-anti-vm-strings-targeting-virtualbox.yml
│   │   └── reference-anti-vm-strings-targeting-virtualpc.yml
│   └── vm-obfuscation/
│       ├── obfuscated-with-dotfuscator.yml
│       └── obfuscated-with-smartassembly.yml
└── obfuscation/
    └── ...
```

| Component | Description |
|-----------|-------------|
| **Top-Level Namespace (TLN)** | Broad category — e.g. `anti-analysis`, `collection`, `persistence` |
| **Namespace** | Sub-category within the TLN |
| **Rule file** | YAML file containing the detection logic for a specific behaviour |

---

## 🎯 Capabilities

A **capability** is a matched rule — it tells you something specific the binary can do.

- The capability name is the same as the rule's YAML file name (e.g. `reference anti-VM strings` → `reference-anti-vm-strings.yml`)
- Each capability links back to its TLN, namespace, and the YAML rule that triggered it

**Example — reading a capability match:**

| Field | Value |
|-------|-------|
| **Capability** | `reference anti-VM strings` |
| **Namespace** | `anti-vm/vm-detection` |
| **Top-Level Namespace** | `anti-analysis` |
| **Rule file** | `reference-anti-vm-strings.yml` |
| **What it means** | CAPA detected that the binary searches for VMware registry keys, VMware Tools presence, or other VM-related artifacts — a common evasion technique used by malware to avoid running inside sandboxes or analyst VMs |

> **Why does malware do this?** If the binary detects it is running in a VM, it can alter or stop its behaviour to avoid analysis — making sandbox detection harder. CAPA flags this so analysts know to account for it.
