# Web Application Fundamentals

## 🌐 Overview

| Layer | Description | Technologies |
|-------|-------------|--------------|
| **Frontend** | Interface the user interacts with | HTML, CSS, JavaScript |
| **Backend** | Structure that makes the app work | Databases, infrastructure, WAF, etc. |

---

## 🔗 URL — Uniform Resource Locator

**Example:** `http://user:password@tryhackme.com:80/view-room?id=1#task3`

| Component | Example | Description |
|-----------|---------|-------------|
| **Scheme** | `http` | Protocol used to access the website |
| **User** | `user:password` | Optional login credentials embedded in the URL |
| **Host/Domain** | `tryhackme.com` | The website being accessed |
| **Port** | `80` | Directs the browser to the right service on the web server |
| **Path** | `/view-room` | Points to the specific file or resource |
| **Query String** | `?id=1` | Starts with `?` — used for search terms or input parameters |
| **Fragment** | `#task3` | Starts with `#` — points to a specific section of the page |

---

## 📨 HTTP Messages

| Message | Direction | Purpose |
|---------|-----------|---------|
| **HTTP Request** | Client → Server | Sent by the user to trigger an action |
| **HTTP Response** | Server → Client | Sent by the server in response |

### Message Structure

| Part | Description |
|------|-------------|
| **Start Line** | Intro of the message — what kind of message, how it should be handled |
| **Headers** | Key-value pairs providing extra info and instructions to client/server |
| **Empty Line** | Separates headers from the body |
| **Body** | Where the data is stored |

---

## 📤 HTTP Request Methods

**Request line format:** `METHOD /path HTTP/version`

| Method | Description |
|--------|-------------|
| `GET` | Fetch data from the server |
| `POST` | Send data to the server |
| `PUT` | Replace or update data on the server |
| `DELETE` | Remove something from the server |
| `PATCH` | Update part of a resource — useful for small changes |
| `HEAD` | Like GET but only retrieves headers — useful for metadata |
| `OPTIONS` | Returns what methods are available for a specific resource |
| `TRACE` | Like OPTIONS but shows allowed methods — used for debugging |
| `CONNECT` | Creates a secure tunnel (e.g. for HTTPS) |

---

## 📋 Request Headers

| Header | Description | Example |
|--------|-------------|---------|
| `Host` | Specifies the web server the request is for | `tryhackme.com` |
| `User-Agent` | Info about the browser making the request | `Mozilla/5.0` |
| `Referer` | URL the request came from | `https://www.google.com` |
| `Cookie` | Stored info sent with the request | `user_type=student; room=intro` |
| `Content-Type` | Format of the data in the request body | `application/json` |

---

## 📬 Response Headers

| Header | Description | Example |
|--------|-------------|---------|
| `Date` | Exact time the response was generated | `Mon, 30 Mar 2026 10:00:00 GMT` |
| `Content-Type` | Kind of content and character set | `text/html; charset=utf-8` |
| `Server` | Server software handling the request — useful for debugging | `nginx` |
| `Set-Cookie` | Sends cookies from server to client to store | `session=abc123` |
| `Cache-Control` | How long the client can cache the response | `max-age=3600` |
| `Location` | Used in redirects — tells client where to go | `https://example.com/new` |

### Cookie Flags

| Flag | Description |
|------|-------------|
| `HttpOnly` | Prevents cookie from being accessed via JavaScript — mitigates XSS attacks |
| `Secure` | Ensures cookie is only transmitted over HTTPS |

---

## 🛡️ Security Headers

Security headers mitigate attacks like XSS by improving web application security.

### CSP — Content Security Policy

Defines trusted sources for content — admins specify which domains are considered safe.

| Directive | Description |
|-----------|-------------|
| `default-src 'self'` | Default policy — only allow resources from the current website |
| `script-src` | Specifies where scripts can be loaded from |
| `style-src` | Specifies where CSS can be loaded from |

### HSTS — HTTP Strict Transport Security

Ensures browsers always connect via HTTPS.

| Option | Description |
|--------|-------------|
| `max-age` | Expiry time in seconds |
| `includeSubDomains` | Applies setting to subdomains (optional) |
| `preload` | Allows website to be included in browser preload lists (optional) |

### Other Security Headers

| Header | Directive | Description |
|--------|-----------|-------------|
| `X-Content-Type-Options` | `nosniff` | Instructs browser not to guess/sniff the MIME type — only use `Content-Type` header |
| `Referrer-Policy` | `no-referrer` | Disables referrer info being sent |
| | `same-origin` | Only sends referrer info when destination is the same origin |
| | `strict-origin` | Sends info only when protocol stays the same |
| | `strict-origin-when-cross-origin` | Like `strict-origin` but sends full URL path within same origin |

---

## 📊 HTTP Status Codes

| Range | Category |
|-------|----------|
| `100–199` | Informational responses |
| `200–299` | Successful responses |
| `300–399` | Redirection messages |
| `400–499` | Client error responses |
| `500–599` | Server error responses |

### Common Status Codes

| Code | Name | Description |
|------|------|-------------|
| `100` | Continue | Server received the first part of the request and is ready for the rest |
| `200` | OK | Request successful — server sending back the requested resource |
| `301` | Moved Permanently | Resource has moved to a new URL permanently |
| `404` | Not Found | Resource doesn't exist at the requested address |
| `500` | Internal Server Error | Server couldn't process the request — something went wrong server-side |
