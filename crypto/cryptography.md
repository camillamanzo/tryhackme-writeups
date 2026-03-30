# Cryptography

## 🔐 Overview

| Term | Description |
|------|-------------|
| **Cipher** | Algorithm used to encrypt data e.g. Caesar cipher (shifting letters left/right by N positions) |
| **Ciphertext** | The unreadable version of a message after encryption |

---

## 🔑 Encryption Types

### Symmetric Encryption (Private Key)
Both parties share the same key for encryption and decryption.

| Algorithm | Key Size | Notes |
|-----------|----------|-------|
| DES | 56-bit | Outdated |
| 3DES | 168-bit | More advanced version of DES |
| AES | 128 / 192 / 256-bit | Current standard |

### Asymmetric Encryption (Public Key)
Uses a key pair — one public, one private. Slower than symmetric due to larger key sizes.

| Algorithm | Key Size | Notes |
|-----------|----------|-------|
| RSA | 2048 / 3072 / 4096-bit | Based on prime factorisation |
| Diffie-Hellman | 2048 / 3072 / 4096-bit | Key exchange protocol |
| ECC | 256-bit | Equivalent strength to 3072-bit RSA with a much smaller key |

---

## ➕ XOR Operation

Short for **Exclusive OR** — compares two bits and returns:
- `1` if the bits are **different**
- `0` if the bits are the **same**

| Input | Result |
|-------|--------|
| `1 XOR 1` | `0` |
| `1 XOR 0` | `1` |
| `0 XOR 0` | `0` |

**Example:** `1010 XOR 1100 = 0110`

**Modulo reminder:** `x % y` returns the remainder e.g. `30%5=0` / `34%5=4` / `38%6=2`

---

## 🔐 Public Key Cryptography

### RSA

Based on the difficulty of **factoring large prime numbers**.

| Variable | Meaning |
|----------|---------|
| P, Q | Two large prime numbers |
| N | Product of P × Q |
| E | Part of the public key (N and E) |
| D | Part of the private key (N and D) |
| M | Plaintext message |
| C | Ciphertext |

### Diffie-Hellman Key Exchange

Establishes a **shared secret over an insecure channel** without ever transmitting the secret directly.

```
A and B want to communicate securely:

1. A generates secret (a), B generates secret (b)
2. Both share common material (c)
3. A sends (ac) to B, B sends (bc) to A
4. A combines received (bc) with own (a) → abc
   B combines received (ac) with own (b) → abc
5. Both now share the same secret (abc) without ever sending it directly
```

---

## 🖥️ SSH Keys

| Concept | Description |
|---------|-------------|
| Authentication | Can use username/password or key pair (RSA by default) |
| Private key | Must always remain private and never shared |

```bash
man ssh-keygen                        # View key generation manual
ssh-keygen -t <algorithm>             # Generate key pair with specific algorithm
# Available algorithms: RSA, DSA, ECDSA, ECDSA-SK, Ed25519, Ed25519-SK
# Example:
ssh-keygen -t Ed25519
```

---

## 📧 PGP & GPG

| Tool | Description |
|------|-------------|
| **PGP** | Pretty Good Privacy — software that encrypts files and digitally signs them |
| **GPG** | GnuPG — open-source implementation of the OpenPGP standard. Used in email for confidentiality and integrity. Also commonly used in CTFs to decrypt files |

```bash
gpg --import backup.key                        # Import a key
gpg --decrypt confidentialMessage.gpg         # Decrypt a message
```

> If a key is passphrase protected, **John the Ripper** + `gpg2john` can be used to crack it.

---

## #️⃣ Hashing

### Hashing Commands

| Command | Description |
|---------|-------------|
| `md5sum *.txt` | Hash all `.txt` files in current directory with MD5 |
| `sha1sum *.txt` | Hash all `.txt` files with SHA1 |
| `sha256sum *.txt` | Hash all `.txt` files with SHA256 |
| `base64 -d decode-this.txt` | Decode a base64-encoded file |

> **Pigeonhole Effect:** All files produce the same size hash digest regardless of input size (1-bit or 200-bit file). This means collisions are theoretically possible.

### Windows Password Hashing
- Passwords are hashed using **NTLM** (variant of MD4) and stored in the **SAM** (Security Account Manager)
- Tools like **Mimikatz** can circumvent SAM to extract hashes

---

## 🐧 Linux Shadow File

Password hashes are stored in `/etc/shadow` — readable by root only.

Each line has 9 fields separated by `:`. The first two are the **login name** and **encrypted password**.

```bash
sudo cat /etc/shadow | grep userName     # Find password hash for a specific user
man 5 shadow                              # More info about the shadow file
man 5 crypt                               # More info about password prefixes
```

### Encrypted Password Field Format: `$prefix$options$salt$hash`

**Example:** `$y$j9T$76$OOSg6...`

| Component | Value | Meaning |
|-----------|-------|---------|
| `$y` | `y` | Hash algorithm (yescrypt) |
| `$j9T` | `j9T` | Parameter passed to the algorithm |
| `$76` | `76` | Salt used |
| `$OOSg6` | `OOSg6` | Hash value |

---

## 🔓 Cracking Passwords

### Online Tools
| Tool | Use |
|------|-----|
| [crackstation.net](https://crackstation.net) | Crack unsalted hashes |
| [hashes.com](https://hashes.com) | Crack unsalted hashes |

### Hashcat

```bash
hashcat -m <hashType> -a <attackMode> <hashFile> <wordList>
```

| Flag | Description | Example |
|------|-------------|---------|
| `-m` | Hash type in numeric form | `-m 1000` (NTLM) |
| `-a` | Attack mode | `-a 0` (straight — tries each password from list) |
| `hashFile` | File containing the hash to crack | `hash.txt` |
| `wordList` | Wordlist to use | `rockyou.txt` |

```bash
# Recover cracked password
cat ~/.local/share/hashcat/hashcat.potfile
```

**Useful references:**
- `man hashcat` for full flag list
- Common searches: *"what hash type is $6"* / *"hashcat hashtype sha512 code -m"*
- [https://hashcat.net/wiki/doku.php?id=example_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

### John the Ripper
Used alongside `gpg2john` to crack passphrase-protected GPG keys.

---

## ✅ Integrity Checking

| Method | Description |
|--------|-------------|
| **HMAC** | Keyed-Hash Message Authentication Code — verifies both the **authenticity** of the sender and the **integrity** of the message |
