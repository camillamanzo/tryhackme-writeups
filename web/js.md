# JavaScript

## 🟨 Overview

JavaScript is an **interpreted language** — code is executed directly in the browser without prior compilation.

---

## 📦 Core Concepts

### Variables

| Keyword | Scope | Notes |
|---------|-------|-------|
| `var` | Function-scoped | Older style — less control |
| `let` | Block-scoped | Preferred for mutable values |
| `const` | Block-scoped | Preferred for values that won't change |

> `let` and `const` give more control over variable scope — prefer them over `var`.

### Data Types

| Type | Example |
|------|---------|
| `string` | `"hello"` |
| `number` | `123`, `3.14` |
| `boolean` | `true`, `false` |

### Built-in Functions

| Function | Description |
|----------|-------------|
| `alert()` | Sends an alert popup to the user |
| `prompt()` | Asks the user for input |
| `confirm()` | Displays OK or Cancel dialog |
| `console.log()` | Outputs to the browser console |

### Loops

| Loop | Description |
|------|-------------|
| `for` | Runs a block a specific number of times |
| `while` | Runs while a condition is true |
| `do...while` | Runs at least once, then checks condition |

### Other Key Concepts

| Concept | Description |
|---------|-------------|
| **Comments** | Written with `//` for single line or `/* */` for multi-line |
| **Request-Response Cycle** | Browser (client) sends a request to a web server, which responds |
| **Control Flow** | The order in which code blocks are executed |

---

## 🖥️ Quick Example

Open the browser console with `CTRL + Shift + I`:

```javascript
let a = 123;
let b = 456;
let res = a + b;
console.log("result: " + res);
// Output: result: 579
```

---

## 🗜️ Minified Files

Minification compresses JS files by removing unnecessary characters and shortening variable names — improves page load time but makes the code non-human-readable.

| Term | Description |
|------|-------------|
| **Minification** | Removing whitespace, comments, and shortening variable names |
| **Obfuscation** | Deliberately making code difficult to read/reverse engineer |
| **Deobfuscation** | Reversing obfuscation to make code readable again |

> **Deobfuscation tool:** [obf-io.deobfuscate.io](https://obf-io.deobfuscate.io/)

---

## 🔗 JS Integration in HTML

| Method | Description | Location |
|--------|-------------|----------|
| **Internal** | JS embedded directly in the HTML document inside `<script>` tags | `<head>` or `<body>` |
| **External** | Separate `.js` file linked from the HTML | External file |

```html
<!-- Internal -->
<script>
  console.log("hello");
</script>

<!-- External -->
<script src="script.js"></script>
```

> **When pen testing:** View the page source to check whether the site uses internal or external JS — external files can be directly downloaded and analysed.

---

## ✅ Security Best Practices

| Practice | Description |
|----------|-------------|
| Server-side validation | Never rely on client-side validation alone — always validate input on the server too |
| Trusted libraries only | Do not add untrusted third-party libraries |
| No hardcoded secrets | API keys, access tokens and credentials must never be written in code |
| Minify and obfuscate | Compress and obfuscate JS code before deploying to production |
