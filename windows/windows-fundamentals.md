# Windows Fundamentals & Active Directory

## 🪟 Windows Basics

### 👤 Users & Permissions

| Task | How To |
|------|--------|
| Check file/folder permissions | Right-click → Properties → Security → Group or Username |
| Check existing users on the system | Start → type: `other user` (Admins can add users here) |
| Local user & group management | `Win + R` → type: `lusrmgr.msc` |
| UAC (User Account Control) | Admin accounts don't have elevated permissions until explicitly needed |

---

### 🛠️ System Tools

| Tool | Access | Description |
|------|--------|-------------|
| Installed programs | Start → Control Panel → Programs → Programs and Features | View and remove installed software |
| Task Manager | `Ctrl + Shift + Esc` | View running processes and performance |
| MSConfig | `Win + R` → `msconfig` → Apps → System Configuration | Advanced troubleshooting tool |
| Startup programs | `Win + R` → `shell:startup` | Verify programs that launch at startup |
| VSS (Volume Shadow Copy) | File Explorer → right-click `C:` → Configure Shadow Copies | Snapshot-based backup tool |

---

### 🖥️ Computer Management — `compmgmt`

**Access:** `Win + R` → type `compmgmt.msc`

| Section | Description |
|---------|-------------|
| Task Scheduler | Schedule automated tasks |
| Event Viewer | View Windows logs, app & service logs, subscriptions |
| Shared Folders | List of folders shared with other users |
| Users and Groups | Manage local users and groups |
| Performance Monitor | Monitor system performance metrics |
| Device Manager | View and configure hardware |
| Storage | Disk management and Windows Server Backup |
| Services and Apps | Right-click a service → Properties for more info |

**Related tools:**

| Tool | Access | Description |
|------|--------|-------------|
| Resource Monitor (Resmon) | `Win + R` → `resmon.exe` | Advanced troubleshooting — CPU, disk, network, memory |
| Registry Editor | `Win + R` → `regedit` | Database of system configurations |

---

### 📋 MSInfo32 — System Information

**Access:** `Win + R` → `msinfo32.exe`

Information gathering tool used to diagnose computer issues. Contains:

| Section | Contents |
|---------|----------|
| Hardware Resources | Hardware details and resource allocation |
| Components | Device components info |
| Software Environment | OS and installed software, environment variables, network connections |

---

## 🔒 Windows Security

**Access:** Start → Windows Security

| Status | Meaning |
|--------|---------|
| 🟢 Green | Device is protected |
| 🟡 Yellow | Safety recommendation available |
| 🔴 Red | Warning — action required |

### Security Features

| Feature | Description |
|---------|-------------|
| Virus & Threat Protection | Run scans, view scan history, manage settings (real-time protection, cloud, etc.) |
| Firewall & Network Protection | Manage firewall for domain, private and public networks. Open Windows Defender Firewall: `Win + R` → `WF.msc` |
| App & Browser Control | Manage Microsoft Defender SmartScreen settings against phishing and malware |
| Device Security | Core isolation in case of attacks, security processor details, TPM |
| Kerberos | Default network authentication protocol — grants tickets to authenticated users |

---

## ⌨️ Windows CMD Commands

| Command | Description | Example |
|---------|-------------|---------|
| `hostname` | Output the computer name | `hostname` |
| `whoami` | Output the currently logged-in user | `whoami` |
| `ipconfig` | Show network address and settings | `ipconfig` |
| `command /?` | Help page for any command | `ipconfig /?` |
| `cls` | Clear the command prompt screen | `cls` |
| `netstat` | Display protocol statistics and current TCP/IP connections | `netstat` |
| `net` | Manage network resources | `net help` for the manual |
| `control /name Microsoft.WindowsUpdate` | Access Windows Update (released every 2nd Tuesday) | — |

---

## 🏢 Active Directory

### Core Concepts

| Concept | Description |
|---------|-------------|
| Windows Domain | Centralised identity and security policy management |
| Domain Controller | Server running Active Directory Domain Services (AD DS) |

### Managing Users

**Access Active Directory Users and Computers:**
Start → type `users` → Active Directory Users and Computers

From here you can:
- Create and manage users, groups, and organisational units (OUs)
- Modify user properties and reset passwords

### PowerShell Commands

**Reset a user's password:**
```powershell
Set-ADAccountPassword AccountName -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

**Force user to change password at next logon:**
```powershell
Set-ADUser -ChangePasswordAtLogon $true -Identity AccountName -Verbose
```

### Group Policy

**Group Policy Management** app allows you to manage group policies across the domain, including account lockout policies, software deployment, and security settings.
