# CyberChef

## 🍳 Overview

**CyberChef** is a web-based application built by GCHQ for performing a wide range of cyber data operations — encoding, decoding, encryption, compression, parsing, and more. It uses a visual "recipe" metaphor: you stack operations together and apply them to your input data to produce a transformed output.

| Access | Link |
|--------|------|
| 🌐 Online | https://gchq.github.io/CyberChef/ |
| 💾 Offline (local copy) | https://github.com/gchq/CyberChef/releases |

> **Why use it?** CyberChef is particularly useful in CTFs, SOC analysis, malware triage, and forensics — anywhere you need to quickly decode, transform, or inspect data without writing custom scripts.

---

## 🧠 Thought Process

```
1. Set a clear objective — what do you want to achieve?
2. Paste or load your data into the Input area
3. Select and arrange operations in the Recipe area
4. Inspect the Output — iterate if needed
```

> **Tip:** If you don't know the encoding, try the **Magic** operation — CyberChef will attempt to auto-detect the format and suggest the right decoding steps.

---

## 🖼️ Interface

CyberChef has four main panels: **Operations**, **Recipe**, **Input**, and **Output**.

---

### Operations Area

The full library of available transformations. Use the search bar to find operations quickly.

| Operation | Category | Description |
|-----------|----------|-------------|
| **From Morse Code** | Encoding | Translates morse code into alphanumeric characters |
| **URL Encode** | Encoding | Encodes special characters into percent-encoding (e.g. space → `%20`) |
| **To Base64** | Encoding | Encodes raw data into an ASCII Base64 string |
| **To Hex** | Encoding | Converts a string into hexadecimal bytes separated by a delimiter |
| **To Decimal** | Encoding | Converts input into an integer (decimal) array |
| **ROT13** | Encryption | Caesar substitution cipher — rotates each character by a set amount (default: 13) |
| **Magic** | Utils | Auto-detects encoding and suggests appropriate operations |

---

### Recipe Area

The recipe is where you build your data processing pipeline — select, arrange, and configure operations in sequence. Operations are applied top to bottom.

| Control | Description |
|---------|-------------|
| **Save Recipe** | Exports the current set of operations as a shareable JSON string |
| **Load Recipe** | Loads a previously saved recipe from a JSON string |
| **Clear Recipe** | Removes all operations from the current recipe |
| **Bake** | Manually runs the data through the recipe |
| **Auto Bake** | When enabled, re-runs the recipe automatically whenever input or operations change |

> **Tip:** Recipes can be saved and shared as URLs — useful for repeatable tasks or sharing with teammates.

---

### Input Area

The input panel is where you load the data you want to process.

| Control | Description |
|---------|-------------|
| **Paste / type / drag** | Standard ways to enter raw text or binary data |
| **Add new input tab** | Open multiple inputs simultaneously without losing previous data |
| **Open file as input** | Upload a single file directly into CyberChef |
| **Open folder as input** | Upload an entire folder for batch processing |
| **Clear input and output** | Resets both panels |
| **Reset pane layout** | Restores the default window sizes |

---

### Output Area

Displays the result of applying all recipe operations to the input data.

| Control | Description |
|---------|-------------|
| **Save output to file** | Downloads the result as a `.dat` file |
| **Copy raw output to clipboard** | Copies the result for use in other tools or documents |
| **Maximise output pane** | Expands the output panel to full width |

> **Output formats:** The output can be rendered as text, hex, Base64, or a file download depending on the operation and data type.

---

## 📂 Categories

CyberChef organises its 300+ operations into categories. The most commonly used:

---

### 🔍 Extractors

Useful for quickly pulling structured data out of large blobs of text or traffic captures:

- Extract IP addresses
- Extract URLs
- Extract email addresses
- Extract MAC addresses
- Extract file paths

---

### 🕐 Date and Time

| Operation | Description |
|-----------|-------------|
| **From Unix Timestamp** | Converts a Unix epoch timestamp (e.g. `1712345678`) to a human-readable datetime string |
| **To Unix Timestamp** | Converts a datetime string back to a Unix epoch timestamp |

> **Unix timestamps** count seconds since 1 January 1970 (UTC). Commonly seen in logs and malware artifacts.

---

### 🔄 Data Format

| Operation | Description | Notes |
|-----------|-------------|-------|
| **From Base64** | Decodes ASCII Base64 back to raw data | Standard — used everywhere (HTTP, email, JWTs) |
| **URL Decode** | Converts percent-encoded values back to plain text | e.g. `%2F` → `/` |
| **From Base85** | Decodes Base85 (also called Ascii85) | ~25% more efficient than Base64 — used in PDFs, some protocols |
| **From Base58** | Decodes Base58 | Like Base64 but removes ambiguous characters (`0`, `O`, `l`, `I`) — used in Bitcoin addresses |
| **To Base62** | Encodes using only `[A-Za-z0-9]` — 62 characters | Used in URL shorteners and token generation |

> **Choosing the right base encoding:** Base64 is the most common. If you see a string that looks like Base64 but won't decode, try Base85 or Base58 — or use the **Magic** operation to let CyberChef figure it out.
