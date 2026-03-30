# Networking Fundamentals

## 🧱 OSI Model

Developed by ISO (International Organisation for Standardisation).

**Acronym:** ***P**lease **D**o **N**ot **T**hrow **S**pinach **P**izza **A**way*

| # | Layer | Description | Examples |
|---|-------|-------------|----------|
| 1 | **Physical** | Physical data transmission — the medium used to transmit signals | Electrical signals, cables |
| 2 | **Data Link** | Data transfer between two nodes on the same network segment | Wi-Fi, Ethernet |
| 3 | **Network** | Sending data between different networks — logical addressing and routing | IP, ICMP, VPN |
| 4 | **Transport** | End-to-end communication between apps on different hosts, data segmentation | TCP, UDP |
| 5 | **Session** | Establishes, maintains and syncs sessions between apps on different hosts | NFS |
| 6 | **Presentation** | Data encoding, compression and encryption | ASCII, TLS |
| 7 | **Application** | Provides network services to end-user applications | HTTP, FTP, DNS |

---

## 🌐 IP Addresses & Subnets

- IP addresses are unique identifiers that allow network communication
- Using the TCP/IP protocol suite, every network-connected device must have an IP address
- An IP address comprises **4 octets of 8 bits**: `0–255.0–255.0–255.0–255` e.g. `192.168.1.1`
- `0` is reserved for the **network address** e.g. `192.168.1.0`
- `255` is reserved for the **broadcast address** e.g. `192.168.1.255` — targets all hosts on the network

### Look Up IP Address

| OS | Command |
|----|---------|
| Windows | `ipconfig` |
| Linux/Unix | `ifconfig` or `ip a s` (ip address show) |

### Subnet Mask

- Can be written as `255.255.255.0` or in CIDR notation e.g. `192.168.1.1/24`
- `/24` means the first 3 octets are fixed — range is `192.168.1.1` to `192.168.1.254`

### Private IP Ranges

| Range |
|-------|
| `10.0.0.0 – 10.255.255.255` |
| `172.16.0.0 – 172.31.255.255` |
| `192.168.0.0 – 192.168.255.255` |

> Private IPs cannot reach or be reached from the internet unless routed through a router with a public IP that supports **NAT**.

---

## 📦 UDP vs TCP

| Feature | UDP | TCP |
|---------|-----|-----|
| Connection required | ❌ No | ✅ Yes |
| Delivery confirmation | ❌ No | ✅ Yes |
| Speed | ✅ Faster | Slower (overhead) |
| Reliability | Lower | Higher |
| Use case | Streaming, DNS | Web, email, file transfer |

### TCP Three-Way Handshake

```
Client          Server
  |--- SYN ------->|   Client initiates connection
  |<-- SYN-ACK ----|   Server responds
  |--- ACK ------->|   Handshake complete
```

---

## 📬 Encapsulation

The process of adding a header to a unit of data before passing it to the layer below. The process is reversed when receiving (de-encapsulation).

**Example — searching on TryHackMe:**

```
[Application]  Browser prepares HTTP request using HTTPS
      ↓
[Transport]    TCP establishes connection to THM web server, sends request
      ↓
[Network]      IP layer adds source IP (your computer) + destination IP (THM server)
      ↓
[Data Link]    Link layer adds header and trailer, packet sent to router
      ↓
[Router]       Removes link layer header/trailer, inspects IP destination, re-routes
               ↑ Repeated at every router until packet reaches target server
```

---

## 📡 Core Protocols

### Telnet
Remote terminal connection protocol — connects to any server listening on a TCP port.

| Server Type | Default Port |
|-------------|-------------|
| Echo server (echoes everything sent) | 7 |
| Daytime server (replies with date/time) | 13 |
| HTTP web server | 80 |

```bash
telnet <ipAddress> <portNumber>   # Connect to a telnet server
# e.g. telnet 10.80.136.162 7
# CTRL + ] to close connection
```

---

### DHCP — Dynamic Host Configuration Protocol
Application-level protocol that relies on UDP. Server listens on port **67**, client sends from port **68**.

**DORA process:**

| Step | Message | Description |
|------|---------|-------------|
| **D**iscover | DHCPDISCOVER | Client broadcasts to find a local DHCP server |
| **O**ffer | DHCPOFFER | Server responds with an available IP address |
| **R**equest | DHCPREQUEST | Client accepts the offered IP |
| **A**cknowledge | DHCPACK | Server confirms the IP address assignment |

---

### ARP — Address Resolution Protocol
Resolves IP addresses to MAC addresses within a local network.

```
Host 192.168.66.1  →  ARP Request: "Who has 192.168.66.2?"
                   ←  ARP Reply: "192.168.66.2 is at [MAC address]"
```

---

### ICMP — Internet Control Message Protocol
Used for diagnostics and error reporting.

| Command | Description |
|---------|-------------|
| `ping` | Tests connectivity to a target and measures RTT (Round-Trip Time) |
| `traceroute` (Linux) / `tracert` (Windows) | Discovers the route from host to target |

---

### Routing Protocols

| Protocol | Description |
|----------|-------------|
| **OSPF** | Open Shortest Path First — selects the most efficient path |
| **EIGRP** | Enhanced Interior Gateway Routing Protocol — Cisco protocol, routers share cost info to find the most efficient path |
| **BGP** | Border Gateway Protocol — primary protocol used on the internet, exchanges routing info between networks |
| **RIP** | Routing Information Protocol — used in small networks, routers share reachable networks and hop counts |

---

### NAT — Network Address Translation
Allows many private IP addresses to share one public IP address. NAT-supporting routers maintain a translation table mapping internal to external addresses.

---

## 🔌 Protocol & Port Reference

### Standard Protocols

| Protocol | Port | Description |
|----------|------|-------------|
| **DNS** | 53 | Domain Name System — maps domain names to IP addresses |
| **HTTP** | 80 | Web traffic |
| **FTP** | 21 | File Transfer Protocol |
| **SMTP** | 25 | Simple Mail Transfer Protocol — mail client to mail server |
| **POP3** | 110 | Post Office Protocol 3 — retrieves email messages |
| **IMAP** | 143 | Internet Message Access Protocol — syncs mailboxes across devices |
| **WHOIS** | — | Publicly available records containing domain registrant contact info |

### Secure Protocols (TLS)

| Protocol | Port | Description |
|----------|------|-------------|
| **HTTPS** | 443 | HTTP over TLS — packets encrypted, private key needed to decrypt |
| **SMTPS** | 465/587 | SMTP with TLS |
| **POP3S** | 995 | POP3 with TLS |
| **IMAPS** | 993 | IMAP with TLS |
| **SSH** | 22 | Secure auth, confidentiality, integrity, tunnelling, X11 forwarding |

> **TLS** (Transport Layer Security) — cryptographic protocol for secure communication. Both client and server require a TLS certificate (cannot be self-signed for public use).

---

### HTTP Methods

| Method | Description |
|--------|-------------|
| `GET` | Retrieves data |
| `POST` | Submits new data |
| `PUT` | Creates a new resource or overwrites existing |
| `DELETE` | Deletes a specified file or resource |

---

### FTP Commands

| Command | Description |
|---------|-------------|
| `USER` | Input username |
| `PASS` | Enter password |
| `RETR` | Download a file from FTP server to client |
| `STOR` | Upload a file from client to FTP server |
| `get fileName.txt -` | Print content of a file (`-` is required) |
| `quit` | Exit FTP session |

```bash
ftp 10.82.148.209       # Connect (use 'anonymous' as user, press Enter for password)
```

---

### HTTP via Telnet

```bash
telnet 10.82.148.209 80   # Connect to HTTP server via Telnet
GET /flag.html            # Retrieve file flag.html
```
