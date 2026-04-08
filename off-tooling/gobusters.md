# GoBuster

## 🔍 Overview

GoBuster is an **offensive reconnaissance tool** that enumerates hidden resources by brute force using wordlists.

### What It Can Enumerate

| Target | Mode |
|--------|------|
| Web directories and files | `dir` |
| DNS subdomains | `dns` |
| Virtual hosts (vhosts) | `vhost` |
| Amazon S3 buckets | `s3` |
| Google Cloud Storage | `s3` |

> **Enumeration:** Listing all available resources — accessible or not. The goal is to discover what exists, then investigate what's reachable.

### VHost vs DNS Mode

| Mode | How It Works |
|------|-------------|
| `vhost` | Navigates to a URL combining the configured hostname (`-u`) with each wordlist entry |
| `dns` | Performs a DNS lookup on the FQDN created by combining the domain (`-d`) with each wordlist entry |

**Installation:** [github.com/OJ/gobuster](https://github.com/OJ/gobuster)

---

## ⚙️ Setup — Ensuring Domains Are Resolvable

```bash
sudo nano /etc/resolv-dnsmasq
# Add (do not replace) as the first line:
nameserver <machineIP>
# Save: CTRL+O → Enter
# Exit: CTRL+X

/etc/init.d/dnsmasq restart     # Restart dnsmasq service
```

---

## 🛠️ Commands & Modes

```bash
gobuster --help         # Help page for the tool
```

| Mode/Command | Description |
|--------------|-------------|
| `dir` | Directory and file enumeration |
| `dns` | DNS subdomain enumeration |
| `vhost` | Virtual host enumeration |
| `fuzz` | Fuzzing mode |
| `s3` | AWS S3 bucket enumeration |
| `tftp` | TFTP enumeration |
| `completion` | Autocomplete script for specified shell |
| `version` | Show current GoBuster version |

---

## 🚩 Global Flags

| Flag | Description |
|------|-------------|
| `--debug` | Enable debug output |
| `--delay <duration>` | Wait time between requests e.g. `1500ms` |
| `--no-color` | Disable colour output |
| `--no-error` | Don't display errors |
| `-z` / `--no-progress` | Don't display progress bar |
| `-o` / `--output <file>` | Write results to a file (default: stdout) |
| `-q` / `--quiet` | Suppress banner and noise |
| `-t` / `--threads <n>` | Number of parallel threads |
| `-v` / `--verbose` | Verbose output — includes errors |
| `-w` / `--wordlist <path>` | Path to wordlist (use `-` for stdin) |
| `--wordlist-offset <n>` | Resume from a given position in the wordlist |

---

## 📁 Dir & File Enumeration

Reveals the directory structure of a website and files within it — useful for finding hidden admin panels, config files, backup files, etc.

```bash
gobuster dir --help     # Show all dir-mode flags
```

### Dir-Specific Flags

| Flag | Description |
|------|-------------|
| `-c` / `--cookies` | Pass a cookie with each request |
| `-x` / `--extensions` | Specify file extensions to search for e.g. `.js`, `.php` |
| `-H` / `--headers` | Pass a custom header with each request |
| `-k` / `--no-tls-validation` | Skip certificate checking for HTTPS targets |
| `-n` / `--no-status` | Don't show status codes in results |
| `-U` / `--username` | Username for authenticated requests |
| `-P` / `--password` | Password for authenticated requests (use with `-U`) |
| `-s` / `--status-codes` | Only show specific status codes e.g. `200` or `300-400` |
| `-b` / `--status-codes-blacklist` | Hide specific status codes |
| `-r` / `--followredirect` | Follow redirects (301, 302, etc.) |

### Examples

```bash
# Basic directory enumeration
gobuster dir -u "http://www.example.thm/" -w /usr/share/wordlists/dirb/small.txt -t 64

# Follow redirects from root directory
gobuster dir -u "http://www.example.thm" -w /usr/share/wordlists/dirb/small.txt -r

# Enumerate a specific subdirectory, search for .js files with 64 threads
gobuster dir -u "http://www.offensivetools.thm/secret" \
  -x .js \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -t 64
# → Discovers: http://offensivetools.thm/secret/flag.js
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `dir` | Directory/file enumeration mode |
| `-u "http://www.example.thm"` | Target URL — `http://` specifies the protocol |
| `-w <path>` | Wordlist to use for enumeration |
| `-x .js` | Also look for files with `.js` extension |
| `-r` | Follow redirect responses |
| `-t 64` | Run 64 threads in parallel |

---

## 🌐 DNS Subdomain Enumeration

Brute-forces subdomains — useful because subdomains are often less patched or hardened than the main domain.

```bash
gobuster dns --help     # Show all dns-mode flags
```

### DNS-Specific Flags

| Flag | Description |
|------|-------------|
| `-d` / `--domain` | Target domain to enumerate (**required**) |
| `-c` / `--show-cname` | Show CNAME records (cannot be used with `-i`) |
| `-i` / `--show-ips` | Include IP addresses of domain and subdomains |
| `-r` / `--resolver` | Configure a custom DNS server for resolving |

### Example

```bash
gobuster dns -d example.thm -w /usr/share/wordlists/dirb/small.txt
```

| Part | Meaning |
|------|---------|
| `dns` | DNS subdomain enumeration mode |
| `-d example.thm` | Target domain |
| `-w <path>` | Wordlist — each entry is combined into a DNS query e.g. `all` → `all.example.thm` |

> `-w` and `-d` are both required for DNS mode.

---

## 🖥️ VHost Enumeration

Brute-forces **virtual hosts** — different websites running on the same IP/machine. Useful when a server hosts multiple domains not listed in DNS.

```bash
gobuster vhost --help   # Show all vhost-mode flags
```

### VHost-Specific Flags

| Flag | Description |
|------|-------------|
| `-u` / `--url` | Base URL — target IP or hostname (**required**) |
| `--domain` | Appends this domain to each wordlist entry |
| `--append-domain` | Appends the base domain to each word in the wordlist |
| `-m` / `--method` | HTTP method to use e.g. `GET`, `POST` |
| `--exclude-length` | Exclude results based on response body length — filters false positives |
| `-r` / `--follow-redirect` | Follow HTTP redirects |

### Example

```bash
gobuster vhost -u "http://10.112.180.194" \
  --domain example.thm \
  -w /usr/share/wordlists/dirb/small.txt \
  --append-domain \
  --exclude-length 250-320
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `vhost` | Virtual host enumeration mode |
| `-u "http://10.112.180.194"` | Browse to this IP (DNS not fully set up) |
| `--domain example.thm` | Sets the second-level (`.example`) and TLD (`.thm`) |
| `--append-domain` | Combines wordlist entries with the domain e.g. `www` → `www.example.thm` |
| `-w <path>` | Wordlist — GoBuster fills the subdomain portion |
| `--exclude-length 250-320` | Filter out responses with body length in this range — removes false positives |

> `-u` and `-w` are both required for VHost mode. `--domain` and `--append-domain` are used when DNS infrastructure is not fully configured.

---

## 📋 Quick Reference

```bash
# Directory enumeration
gobuster dir -u "http://target.thm" -w /usr/share/wordlists/dirb/small.txt -t 64

# Directory enumeration — specific file extensions
gobuster dir -u "http://target.thm" -x .php,.js,.txt -w /usr/share/wordlists/dirb/common.txt -t 64

# DNS subdomain enumeration
gobuster dns -d target.thm -w /usr/share/wordlists/dirb/small.txt

# VHost enumeration
gobuster vhost -u "http://10.10.10.1" --domain target.thm -w /usr/share/wordlists/dirb/small.txt --append-domain --exclude-length 250-320
```
