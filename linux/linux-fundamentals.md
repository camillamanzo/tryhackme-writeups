# Linux Fundamentals

## 🖥️ Linux Commands

| Command | Description | Example |
|---------|-------------|---------|
| `echo` | Output any text | `echo hello` |
| `whoami` | Find out the user currently logged in | `whoami` |
| `man` | Show manual page for a command | `man ls` |
| `su` | Switch user | `su user2` then insert password when prompted |
| `ls` | List everything in current directory (hidden files excluded) | `ls` |
| `ls -a` | List everything including hidden files | `ls -a` |
| `ls -la` | Check permissions to access directories | `ls -la /var/log` |
| `ls --help` | List all available `ls` options | `ls --help` |
| `cd` | Change current directory | `cd files` |
| `cat` | Output contents of a file | `cat todo.txt` |
| `pwd` | Print full path to current directory | `pwd` |
| `find` | List directories and their contents | `find` |
| `find -name` | Find a specific file | `find -name todo.txt` \| `find -name *.txt` |
| `grep` | Search contents of files | `grep "text" todo.txt` |
| `grep -R` | Recursive search through all files in subdirectories | `grep -R "TEXT" /folderName/` |
| `wget` | Download files via HTTP | `wget https://link-to-file` |

---

## ⚙️ Linux Process Commands

| Command | Description | Example |
|---------|-------------|---------|
| `ps` | View active processes under current user | `ps` |
| `ps aux` | View all processes including other users and system processes | `ps aux` |
| `top` | Real-time stats, refreshes every 10 seconds | `top` |
| `kill PID` | Kill a process by its PID | `kill 36` |
| `systemctl [action] [process]` | Interact with a process — start, stop, enable, disable, status | `systemctl start apache2` |

### `kill` Signal Types
| Signal | Behaviour |
|--------|-----------|
| `SIGTERM` | Kill the process but allow it to do cleanup first |
| `SIGKILL` | Kill the process immediately, no cleanup |
| `SIGSTOP` | Stop/suspend the process |

---

## 📁 Linux Filesystem Commands

| Command | Description | Example |
|---------|-------------|---------|
| `touch` | Create a file | `touch newFile` |
| `mkdir` | Create a folder | `mkdir newDirectory` |
| `cp` | Copy a file or folder | `cp fileName1 fileName2` |
| `mv` | Move or rename a file or folder | `mv fileName1 newFileName` |
| `rm` | Remove a file | `rm newFile` |
| `rm -R` | Remove a directory | `rm -R newDirectory` |
| `file` | Determine the type of a file | `file newFile` |
| `cat` | Output file content | `cat fileName` |
| `nano` | Create or edit a file directly in the terminal | `nano fileName` |
| `wget` | Download files via HTTP (connect to Python server first) | `wget https://link-to-file` |
| `python3 -m http.server` | Start a local HTTP server on a separate tab, then use `wget` from another | run in tab 1, `wget` in tab 2 |
| `scp` | Secure copy — like `cp` but with authentication and encryption | `scp file user@IP:/path` |
| `head -N` | Read only the first N lines of a file | `head -20 file.txt` |

---

## 🔧 Shell Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `&` | Run a command in the background | `longRunningCommand &` |
| `&&` | Chain commands — run the second only if the first succeeds | `command1 && command2` |
| `>` | Redirect output to a file, overwriting it | `echo text1 > file1` |
| `>>` | Redirect output and append to a file (does not overwrite) | `echo text2 >> file1` |

---

## 🔐 SSH

```bash
ssh username@IP
# Example:
ssh tryhackme@10.82.167.65
# Insert password when prompted
```

---

## 📂 Important Linux Directories

| Directory | Description |
|-----------|-------------|
| `/etc` | Common location for system configuration files used by the OS |
| `/var` | Variable data — stores data accessed or written by services/applications (e.g. `/var/log` for logs) |
| `/root` | Home directory for the root user |
| `/tmp` | Volatile directory, similar to RAM — content is cleared on reboot |
