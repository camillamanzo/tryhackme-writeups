# FlareVM

## 🔥 Overview

**FlareVM** is a Windows-based security distribution developed by Mandiant's FLARE team. Unlike REMnux (Linux-based), FlareVM runs on Windows — making it ideal for analysing Windows malware in its native environment. It is a curated collection of cybersecurity tools covering reverse engineering, forensics, network analysis, and more, all pre-installed and configured.

> **REMnux vs FlareVM:** REMnux is Linux-based and excels at static file analysis and network simulation. FlareVM is Windows-based and is better suited for dynamic analysis of Windows binaries in their native OS.

---

## 🧰 Tool Categories

### 🔧 Reverse Engineering & Debugging

| Tool | Description |
|------|-------------|
| **Ghidra** | NSA-developed open source reverse engineering suite — disassembly, decompilation, scripting |
| **x64dbg** | Open source debugger for x64 and x32 Windows binaries |
| **OllyDbg** | Debugger for reverse engineering at the assembly level (x86) |
| **Radare2** | Open source platform for reverse engineering, binary analysis, and exploitation |
| **Binary Ninja** | Commercial tool for disassembling and decompiling binaries — clean UI, scriptable |
| **PEiD** | Detects packers, cryptors, and compilers used on PE files |

---

### 🔍 Disassemblers & Decompilers

Help analysts understand malicious software by breaking it down into a human-readable format.

| Tool | Description |
|------|-------------|
| **CFF Explorer** | PE editor — analyses and edits Portable Executable files, inspects headers and structure |
| **Hopper Disassembler** | Debugger, disassembler, and decompiler in one tool |
| **RetDec** | Open source decompiler for machine code — converts binaries back to C-like pseudocode |

---

### 📊 Static & Dynamic Analysis

**Static analysis** — inspecting code without executing it. **Dynamic analysis** — observing behaviour as it runs.

| Tool | Description |
|------|-------------|
| **Process Hacker** | Memory editor and process watcher — advanced alternative to Task Manager |
| **PEview** | Lightweight PE file viewer for header and structure analysis |
| **Dependency Walker** | Displays a PE's DLL dependencies — useful for spotting unusual or missing libraries |
| **DIE (Detect It Easy)** | Detects packers, compilers, and cryptors — broader coverage than PEiD |

---

### 🕵️ Forensics & Incident Response

| Tool | Description |
|------|-------------|
| **Volatility** | RAM dump analysis framework for memory forensics |
| **Rekall** | Alternative memory forensics framework, focused on incident response |
| **FTK Imager** | Disk image acquisition and analysis — creates forensic copies of drives |

---

### 🌐 Network Analysis

| Tool | Description |
|------|-------------|
| **Wireshark** | Network protocol analyser — captures and inspects traffic at the packet level |
| **Nmap** | Network mapping and vulnerability detection |
| **Netcat** | Read and write raw data across network connections — useful for testing and pivoting |

---

### 📁 File Analysis

| Tool | Description |
|------|-------------|
| **FileInsight** | Hex editor with scripting support for inspecting and editing binary files |
| **Hex Fiend** | Fast, lightweight hex editor |
| **HxD** | Binary file viewing and editing with a hex editor — supports memory editing |

---

### ⚙️ Scripting & Automation

| Tool | Description |
|------|-------------|
| **Python** | Pre-installed with malware analysis and automation modules |
| **PowerShell Empire** | Framework for PowerShell-based post-exploitation and automation |

---

### 🪟 Sysinternals Suite

A collection of advanced Windows utilities for managing, troubleshooting, and diagnosing Windows systems.

| Tool | Description |
|------|-------------|
| **Autoruns** | Shows all executables configured to run at system boot — useful for persistence detection |
| **Process Explorer** | Advanced process viewer — shows parent/child relationships, loaded DLLs, and paths |
| **Process Monitor (Procmon)** | Monitors and logs real-time process, file system, registry, and network activity |

---

## 🔬 Key Tools — Deep Dive

### PEstudio — Static PE Analysis

**PEstudio** analyses Portable Executable files without running them — ideal as a first-pass triage tool.

| Feature | What to Look For |
|---------|-----------------|
| **MD5 / SHA1 hashes** | Compare against VirusTotal and threat intel databases to check for known malware |
| **Version / metadata** | File descriptions can be faked — e.g. `description = "REGEDIT"` implies a registry editor, but real `regedit.exe` lives in `System32`. A mismatch is suspicious |
| **Rich header** | Absent rich header may indicate the file is **packed or obfuscated** to defeat static analysis |
| **Functions / Imports** | Lists API calls imported by the file — blacklisted calls reveal likely behaviour |

**Notable suspicious API calls:**

| API Call | What It Suggests |
|----------|-----------------|
| `SetUseShellExecute` | Allows the process to use the OS shell to launch other processes — common in droppers |
| `VirtualAlloc` / `VirtualProtect` | Memory allocation with execute permissions — indicator of shellcode injection |
| `CreateRemoteThread` | Spawning threads in another process — classic process injection technique |
| `WinExec` / `ShellExecute` | Executing external commands or files |

---

### FLOSS — FLARE Obfuscated String Solver

**FLOSS** extracts and de-obfuscates strings from malware using static analysis — going beyond simple `strings` output by recovering encoded, XOR'd, or stack-built strings that would otherwise be invisible.

> FLOSS also outputs Python scripts compatible with IDA Pro and Binary Ninja for further analysis.

```powershell
# Navigate to the FLOSS directory
cd C:\path\to\floss

# Run FLOSS and save output to a text file
FLOSS.exe .\malware.exe > malware_floss.txt

# Open the output file
notepad malware_floss.txt
```

> **Where to look:** Scroll toward the **end** of the output file — the most useful decoded strings (URLs, file paths, registry keys, commands) tend to appear there.

---

### Process Explorer — Process & DLL Inspection

**Process Explorer** gives deep visibility into running processes — far beyond Task Manager.

```
1. Run the suspicious executable
2. Open Process Explorer and locate the process by name
3. Note the PID (Process ID)
4. Right-click the process → Properties
5. Navigate to the TCP/IP tab — check destination IPs and connection state
```

| Tab | What to Check |
|-----|--------------|
| **Image** | Full path of the executable — unexpected locations (e.g. `%TEMP%`, `AppData`) are suspicious |
| **Strings** | Printable strings in memory — may reveal URLs, registry keys, or commands |
| **TCP/IP** | Active or listening connections — unknown external IPs are an IOC |
| **DLLs** | Loaded libraries — unexpected or unsigned DLLs may indicate injection |

---

### Procmon — Real-Time Activity Monitoring

**Process Monitor** records all process, file system, registry, and network activity in real time — used to verify and expand on findings from Process Explorer.

```
1. Run the suspicious executable again
2. Open Procmon
3. Apply a filter: Ctrl+L
   → Column:    Process Name
   → Relation:  contains
   → Value:     <process name or keyword>
   → Action:    Include
4. Click Add → Apply
5. Watch for connections to unknown external IP addresses, unexpected file writes, or registry modifications
```

**Key things to watch for in Procmon:**

| Activity | What It May Indicate |
|----------|---------------------|
| Network connections to unknown IPs | C2 (command and control) communication |
| File writes to `%TEMP%` or `AppData` | Dropper behaviour — writing a payload to disk |
| Registry modifications under `HKCU\Run` or `HKLM\Run` | Persistence mechanism |
| Process spawning (e.g. `cmd.exe`, `powershell.exe`) | Execution of secondary payloads or commands |

---

### HxD — Hex Editor

**HxD** lets you examine and edit raw binary files and memory contents at the byte level.

- View and edit file contents in hexadecimal and ASCII side-by-side
- Search for byte patterns, strings, or specific values
- Compare two files side-by-side to spot differences
- Edit live process memory — useful for patching anti-analysis checks during dynamic analysis

---

### Wireshark — Network Traffic Analysis

Capture and inspect all network traffic generated by a running sample.

- Filter by IP, protocol, or port to isolate malware traffic
- Follow TCP/UDP streams to reconstruct full conversations
- Export objects (files transferred over HTTP, FTP, etc.)
- Identify C2 protocols, DNS queries, and data exfiltration attempts

> **Tip:** Run Wireshark *before* launching the sample so you capture traffic from the first moment of execution — some malware beacons immediately on startup.
