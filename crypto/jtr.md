# John the Ripper

## 🔨 Overview & Resources

| Resource | Link |
|----------|------|
| Wordlist repository (SecLists) | [github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists) |
| Kali Linux wordlist directory | `/usr/share/wordlists/` |
| Hash identifier (online) | [hashes.com/en/tools/hash_identifier](https://hashes.com/en/tools/hash_identifier) |
| Hash identifier (script) | [hash-id.py](https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py) |
| John the Ripper docs | [openwall.com/john](https://www.openwall.com/john/) |
| Hashcat example hashes | [hashcat.net/wiki](https://hashcat.net/wiki/doku.php?id=example_hashes) |

### Download Hash Identifier

```bash
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
python3 hash-id.py    # Run and enter hash to identify type
```

---

## ⚙️ Basic Syntax

```bash
john options filePath                          # Generic command
john --wordlist=<wordFilePath> <hashFile>      # Automatic cracking with wordlist
john --format=<format> --wordlist=<wordFilePath> <hashFile>   # Specify hash format
```

### Useful Commands

| Command | Description |
|---------|-------------|
| `john --list=formats` | List all supported hash formats |
| `john --list=formats \| grep -iF "md5"` | Search for a specific hash format |
| `john --show <hashFile>` | Show cracked passwords for a file |
| `cat /home/user/src/john/run/john.pot` | View all previously cracked passwords |

---

## 🔐 Hash Types

### NT Hash (Windows)

NTLM / NTHash is the Windows format for storing user passwords. Obtained by dumping the SAM database with **Mimikatz** or the Active Directory database `NTDS.dit`.

```bash
john --show /home/user/src/john/run/john.pot
```

### Shadow File (Linux)

`/etc/shadow` contains password hashes, last password change, expiration info, etc.

Combine `/etc/shadow` with `/etc/passwd` from the target machine using `unshadow`:

```bash
unshadow local_passwd local_shadow > unshadowed.txt
john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
```

---

## 🔁 Single Crack Mode

John uses the **username** to generate possible passwords by slightly modifying it (letter substitutions, capitalisation, etc.).

> **GECOS** — General Electric Comprehensive Operating System: the 5th colon-separated field in `/etc/shadow`, containing user info like full name, office number, telephone number. John uses this to inform guesses.

```bash
john --single --format=<format> <hashFile>
# Example:
john --single --format=raw-sha256 hashes.txt
```

> **File format requirement:** Hash file must be in `username:hashedPassword` format.
> Use `nano fileName` to prepend the username if needed.
> e.g. `cjdeichnedic` → `mike:cjdeichnedic`

---

## 📐 Custom Rules

Custom rules tell John how to mutate wordlist entries dynamically — useful for mimicking organisation password complexity policies.

**Config file location:**
- TryHackMe: `/etc/john/john.conf`
- General: `/opt/john/john.conf`

**Reference:** [openwall.com/john/doc/RULES.shtml](https://www.openwall.com/john/doc/RULES.shtml)

```bash
john --wordlist=rockyou.txt --rule=<ruleName> hash.txt
```

### Rule Modifiers

| Modifier | Description |
|----------|-------------|
| `Az` | Append to the end of the word |
| `A0` | Prepend to the beginning of the word |
| `c` | Capitalise the character positionally |

### Character Sets

| Set | Includes |
|-----|----------|
| `[0-9]` | Numbers 0–9 |
| `[0]` | Only the number 0 |
| `[A-z]` | Both uppercase and lowercase letters |
| `[A-Z]` | Uppercase letters only |
| `[a-z]` | Lowercase letters only |
| `[a]` | Only the letter a |
| `[!£$%@]` | Specified symbols |

### Example Rule

Assuming `polopassword` is in the wordlist — creates passwords like `Polopassword1!`:

```
[List.Rules:PoloPassword]
cAz"[0-9][!£$%@]"
```

| Component | Effect |
|-----------|--------|
| `c` | Capitalise the first letter |
| `Az` | Append to the end |
| `[0-9]` | Append a digit 0–9 |
| `[!£$%@]` | Append one of these symbols |

---

## 📦 Protected ZIP Files

```bash
# Step 1 — Convert ZIP to hash format
zip2john zipFile.zip > zip_hash.txt

# Step 2 — Crack with John
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt

# Step 3 — View cracked password
cat /home/user/src/john/run/john.pot

# Step 4 — Unzip using cracked password
unzip zipFile.zip
```

---

## 📦 Protected RAR Archives

RAR archives are compressed files/folders created by WinRAR.

```bash
# Step 1 — Convert RAR to hash format
rar2john rarFile.rar > rar_hash.txt
# or:
/opt/john/rar2john rarFile.rar > rar_hash.txt

# Step 2 — Crack with John
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt

# Step 3 — View cracked password
cat /home/user/src/john/run/john.pot

# Step 4 — Extract using cracked password
unrar rarFile.rar
```

---

## 🔑 SSH Private Key Passwords

`id_rsa` is the private key used to authenticate SSH sessions. If it's passphrase-protected, John can crack it.

```bash
# Step 1 — Convert id_rsa to hash format
ssh2john id_rsa > hash.txt
# If that doesn't work, try:
python3 /opt/john/ssh2john.py id_rsa > hash.txt
python /usr/share/john/ssh2john.py id_rsa > hash.txt

# Step 2 — Crack with John
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Step 3 — View cracked passphrase
cat /home/user/src/john/run/john.pot
```

---

## 📋 Quick Reference — Conversion Tools

| File Type | Conversion Tool | Command |
|-----------|----------------|---------|
| Linux shadow file | `unshadow` | `unshadow passwd shadow > out.txt` |
| ZIP archive | `zip2john` | `zip2john file.zip > out.txt` |
| RAR archive | `rar2john` | `rar2john file.rar > out.txt` |
| SSH private key | `ssh2john` | `ssh2john id_rsa > out.txt` |
