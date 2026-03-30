# Windows Command Line & PowerShell

## 💻 Windows CMD

### System Info

| Command | Description | Example |
|---------|-------------|---------|
| `set` | Check your path from the command line (run from `C:`) | `set` |
| `ver` | Determine OS version | `ver` |
| `systeminfo` | List system info — OS, hardware, processor, memory, etc. | `systeminfo` |
| `\| more` | View long output page by page (press spacebar to scroll) | `driverquery \| more` |
| `ipconfig` | Show IP address, subnet mask, default gateway | `ipconfig` |
| `ipconfig /all` | Extended network info — DNS servers, DHCP, MAC address | `ipconfig /all` |

---

### 🌐 Network Troubleshooting

| Command | Description | Example |
|---------|-------------|---------|
| `ping` | Check if a server is reachable | `ping google.com` |
| `tracert` | Trace the network route (routers) to reach a target | `tracert google.com` |
| `nslookup` | Return IP address from a hostname or domain | `nslookup google.com` |
| `netstat` | Display current network connections and listening ports | `netstat` |
| `netstat -a` | Established connections and listening ports | `netstat -a` |
| `netstat -b` | Show program associated with each port/connection | `netstat -b` |
| `netstat -o` | Show process ID (PID) associated with each connection | `netstat -o` |
| `netstat -n` | Use numerical form for addresses and port numbers | `netstat -n` |
| `netstat -abon` | Combines all four options above | `netstat -abon` |
| `chkdsk` | Check file system and disk volumes for errors and bad sectors | `chkdsk` |
| `driverquery` | Display a list of installed device drivers | `driverquery` |
| `sfc /scannow` | Scan system files for corruption and repair if possible | `sfc /scannow` |

---

### 📁 File Management

| Command | Description | Example |
|---------|-------------|---------|
| `cd` | Display current drive and directory (no parameters) | `cd` |
| `dir` | Show child directories | `dir` |
| `dir -a` | Display hidden files | `dir -a` |
| `dir -s` | Display files in current directory and all subdirectories | `dir -s` |
| `mkdir` | Create a directory | `mkdir DirName` |
| `rmdir` | Remove a directory | `rmdir DirName` |
| `type` / `more` | View text file content | `type file.txt` \| `more file.txt` |
| `copy` | Copy a file to another location | `copy file.txt file2.txt` \| `copy *.txt C:\Text` |
| `move` | Move a file | `move file.txt C:\Other` |
| `del` / `erase` | Delete a file | `del file.txt` |

---

### ⚙️ Task & Process Management

| Command | Description | Example |
|---------|-------------|---------|
| `tasklist` | List all running processes | `tasklist` |
| `tasklist /?` | Help page to check available filters | `tasklist /?` |
| `tasklist /FI` | Filter processes by image name | `tasklist /FI "imagename eq sshd.exe"` |
| `taskkill /PID` | Kill a process by its PID | `taskkill /PID 2345` |

---

## 🔷 Windows PowerShell

### General Info

- Designed for **task automation** and **configuration management**
- Access from Windows: `Win + R` → type `powershell`, or from CMD type `powershell`
- Commands (cmdlets) follow a **Verb-Noun** naming convention e.g. `Get-Content`, `Set-Location`
- Additional cmdlets can be downloaded from online repositories
- **Piping** — use the output of one command as input for another with the `|` symbol

---

### 📋 General Commands

| Command | Description | Example |
|---------|-------------|---------|
| `Get-Command` | List all cmdlets, functions, aliases and scripts | `Get-Command` |
| `Get-Command -CommandType` | Get only a specific type | `Get-Command -CommandType "Function"` |
| `Get-Help` | Info about a specified cmdlet | `Get-Help Get-Process` |
| `Get-Help -Examples` | Add examples to the help output | `Get-Help Get-Process -Examples` |
| `Get-Alias` | List all available aliases | `Get-Alias` |
| `Find-Module -Name` | Find modules to download (`*` wildcard supported) | `Find-Module -Name "PowerShell*"` |
| `Install-Module -Name` | Install a module with new cmdlets | `Install-Module -Name "PowerShellGet"` |

---

### 📁 Filesystem Commands

| Command | Description | Example |
|---------|-------------|---------|
| `Get-ChildItem -Path` | List files and directories | `Get-ChildItem -Path ".\Path"` |
| `Set-Location -Path` | Change current directory | `Set-Location -Path ".\Path"` |
| `New-Item -Path -ItemType` | Create a new file or directory | `New-Item -Path ".\file.txt" -ItemType "file"` |
| `Remove-Item -Path` | Remove a file or directory | `Remove-Item -Path ".\file.txt"` |
| `Copy-Item -Path -Destination` | Copy an item to a new location | `Copy-Item -Path .\file.txt -Destination .\backup\` |
| `Move-Item -Path -Destination` | Move an item to a new location | `Move-Item -Path .\file.txt -Destination .\archive\` |
| `Get-Content -Path` | Output a file's content | `Get-Content -Path ".\file.txt"` |

---

### 🔗 Piping

| Command | Description | Example |
|---------|-------------|---------|
| `Sort-Object` | Sort results by a property | `Get-ChildItem \| Sort-Object Length` |
| `Where-Object -Property` | Filter results by a property value | `Get-ChildItem \| Where-Object -Property "Extension" -eq ".txt"` |
| `Select-Object` | Output only specified properties | `Get-ChildItem \| Select-Object Name, Length` |

**Chaining example** — find the largest file in a directory:
```powershell
Get-ChildItem | Sort-Object Length -Descending | Select-Object -First 1
```

**Comparison operators:**

| Operator | Meaning |
|----------|---------|
| `-eq` | Equal |
| `-ne` | Not equal |
| `-gt` | Greater than |
| `-ge` | Greater than or equal |
| `-lt` | Less than |
| `-le` | Less than or equal |
| `-like` | Contains / wildcard match |

---

### 🖥️ System & Network Info

| Command | Description |
|---------|-------------|
| `Get-ComputerInfo` | OS info, hardware specs, BIOS details |
| `Get-LocalUser` | List all user accounts on the system |
| `Get-NetIPConfiguration` | Network info — IP addresses, DNS servers, gateway |
| `Get-NetIPAddress` | Details about all IP addresses configured on the system |

---

### 📊 Real-Time System Analysis

| Command | Description | Example |
|---------|-------------|---------|
| `Get-Process` | View all currently running processes | `Get-Process` |
| `Get-Service` | Info about service status — running, stopped, paused | `Get-Service` |
| `Get-NetTCPConnection` | View TCP connections and local/remote endpoints | `Get-NetTCPConnection` |
| `Get-FileHash` | Generate file hashes to detect integrity or tampering | `Get-FileHash file.txt` |
| `Get-Service \| Where-Object` | Query services by display name | `Get-Service \| Where-Object DisplayName -like "A Merry*"` |

---

### 📝 Scripting Use Cases

| Role | Use Cases |
|------|-----------|
| 🔵 **Blue Team** | Log analysis automation, anomaly detection, IOC extraction, malware reverse engineering, intrusion scanning |
| 🔴 **Red Team** | System enumeration automation, remote command execution |
| 🛠️ **Sys Admins** | Integrity checks, configuration management, network security, policy enforcement, system health monitoring, incident response |

**Remote command execution example:**
```powershell
Invoke-Command -ComputerName Server01 -Credential Domain01\User01 -ScriptBlock { Get-Culture }
```
> Runs `Get-Culture` on `Server01` using `User01`'s credentials. PowerShell prompts for the password, executes the command remotely, and returns the result.
