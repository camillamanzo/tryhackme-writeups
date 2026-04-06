# Burp Suite

## 🕷️ Overview

Burp Suite is a **Java-based framework** for web application penetration testing. It captures and allows manipulation of HTTP/HTTPS traffic before it reaches the target server or browser.

### Editions

| Edition | Description |
|---------|-------------|
| **Community** | Free — core features available, rate-limited fuzzer/brute-forcer |
| **Professional** | Unrestricted — automated vulnerability scanner, unlimited fuzzer/brute-forcer, project saving |
| **Enterprise** | Primarily used for continuous scanning at scale |

---

## 🧰 Features

| Tool | Description |
|------|-------------|
| **Proxy** | Intercept and modify HTTP/HTTPS requests and responses |
| **Repeater** | Capture, modify and resend requests multiple times — great for manual testing |
| **Intruder** | Spray endpoints with requests — used for brute-forcing and fuzzing |
| **Decoder** | Transform data — decode captured info or encode payloads before sending |
| **Comparer** | Compare two pieces of data at word or byte level |
| **Sequencer** | Assess the randomness of tokens e.g. session cookies — useful for finding logic flaws |
| **Extensions** | Add custom functionality — written in Java, Python or Ruby |
| **Burp Extender** | Module for quickly loading extensions |
| **BApp Store** | Marketplace to download third-party extensions |

**Download:** [portswigger.net/burp/releases](https://portswigger.net/burp/releases)
> Kali Linux: Burp Suite comes pre-installed. If not: `sudo apt install burpsuite`

---

## 🖥️ Dashboard

The dashboard is divided into four quadrants (top-left → clockwise):

| Quadrant | Name | Description |
|----------|------|-------------|
| Top-left | **Tasks** | Define background tasks. Default: *Live Passive Crawl* — logs pages visited |
| Top-right | **Event Log** | Info about actions performed by Burp Suite e.g. starting the proxy |
| Bottom-right | **Issue Activity** | *(Pro only)* Vulnerabilities found by the scanner, ranked by severity |
| Bottom-left | **Advisory** | Detailed vulnerability info with references and remediations — exportable as a report |

> `?` icons open a help window specific to that section.

---

## 🧭 Navigation

| Feature | Description |
|---------|-------------|
| **Module Selection** | Top menu bar — click to switch between modules |
| **Sub-tabs** | Second menu bar below the main one — module-specific sub-tasks |
| **Detaching Tabs** | View modules in separate windows via the application menu |

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `CTRL + Shift + D` | Dashboard |
| `CTRL + Shift + T` | Target tab |
| `CTRL + Shift + P` | Proxy tab |
| `CTRL + Shift + I` | Intruder tab |
| `CTRL + Shift + R` | Repeater tab |

### Settings

| Setting Type | Description |
|--------------|-------------|
| **Global settings** | Affect the entire Burp Suite installation — applied every time the app starts |
| **Project settings** | Specific to a project/session — lost on close in Community edition |

> **Note:** Burp Suite Community does not support saving projects — all settings are lost when the app is closed.

---

## 🔀 Burp Proxy

The proxy sits between your browser and the target server, giving you full control over all traffic.

| Feature | Description |
|---------|-------------|
| **Intercept requests** | Requests appear in the Proxy tab — forward, drop, edit or send to other tools |
| **Capture & logging** | All traffic is logged even when interception is turned off |
| **WebSocket support** | Captures and logs WebSocket communication |
| **HTTP/WebSocket history** | View past requests in the sub-tabs |
| **Response interception** | Off by default — enable via *Intercept responses based on following rules* checkbox |
| **Match and replace** | Use regex to automatically modify requests |

---

## 🔌 Setting Up the Proxy (Firefox)

### Step 1 — Install FoxyProxy
[addons.mozilla.org/en-US/firefox/addon/foxyproxy-basic](https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-basic/)

### Step 2 — Configure FoxyProxy

| Field | Value |
|-------|-------|
| Title | `Burp` |
| Proxy IP | `127.0.0.1` |
| Port | `8080` |

### Step 3 — Activate

1. Save config and click the FoxyProxy icon in Firefox
2. Select your `Burp` config to redirect traffic through `127.0.0.1:8080`
3. In Burp Suite: Proxy tab → click **Intercept is on**
4. Open Firefox and visit any website — the request should appear in Burp Suite

> When the proxy config is active, the **browser will hang** whenever you make a request — this is expected. The request is waiting to be forwarded in Burp Suite.

### Step 4 — Fix TLS Certificate Issues (HTTPS sites)

```
1. Visit http://burp/cert in Firefox → download cacert.der
2. Go to about:preferences in Firefox → View Certificates
3. Import cacert.der
4. Check: "Trust this CA to identify websites"
```

> Once the PortSwigger CA cert is trusted, TLS-enabled HTTPS sites will be accessible through the proxy.

---

## 🗺️ Site Map & Issue Definitions

| Feature | Description |
|---------|-------------|
| **Site Map** | Maps the target web app as a tree — discover pages, APIs and endpoints |
| **Issue Definitions** | Full list of web vulnerabilities with descriptions and references — useful for reports |

### Scoping (Limiting What Gets Proxied)

```
Target tab → right-click target → Add to Scope
Proxy Settings → select "And URL is in target scope"
```

> Scoping prevents Burp Suite from intercepting and logging traffic outside your target — keeps things clean during assessments.

---

## ⚔️ XSS Attack Example

Demonstrating a reflected XSS attack using Burp Proxy:

```
1. Activate Burp Proxy intercept
2. Fill in and submit a form on the target site
3. Find the intercepted request in the Proxy tab
4. Modify a field with an XSS payload:
   <script>alert("Successful XSS")</script>
5. URL-encode the payload: select it → CTRL+U
6. Forward the request
7. If successful → a popup alert appears on the page
```

> **Why URL-encode?** Special characters like `<`, `>` and `"` have meaning in HTTP. URL-encoding (`%3C`, `%3E`, `%22`) ensures the payload is transmitted safely without being stripped or misinterpreted by the server.
