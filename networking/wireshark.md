# Wireshark

## 🦈 Overview

Wireshark is a **traffic analyser tool** used to capture and inspect network packets in real time.

### Use Cases

| Use Case | Description |
|----------|-------------|
| Network troubleshooting | Detect network load failure points and connectivity issues |
| Security anomaly detection | Identify rogue hosts, bad port usage, suspicious traffic |
| Protocol investigation | Examine response codes, payload data, and protocol behaviour |

---

## 🎨 Colour Coding

**Customise:** View → Colouring Rules

| Colour | Traffic Type |
|--------|-------------|
| 🟢 Green | HTTP |
| 🔵 Light Blue | UDP |
| 🟣 Lilac | TCP |

---

## 🔬 Packet Dissection

Investigates packet details by decoding available protocols and fields. Click a packet to open details, double-click to open in a new window.

### The Seven Layers of a Packet

| Layer | OSI Layer | Description |
|-------|-----------|-------------|
| **Frame** | Layer 1 — Physical | Frame/packet details at the physical layer |
| **Source (MAC)** | Layer 2 — Data Link | Source and destination MAC addresses |
| **Source (IP)** | Layer 3 — Network | Source and destination IPv4 addresses |
| **Protocol** | Layer 4 — Transport | Protocol used (TCP/UDP) and source/destination ports |
| **Protocol Errors** | Layer 4 — Transport | Segments that needed to be reassembled |
| **Application Protocol** | Layer 7 — Application | Specific protocol details e.g. HTTP, FTP |
| **Application Data** | Layer 7 — Application | Application-specific payload data |

---

## 🖥️ UI Commands

| Action | How To |
|--------|--------|
| Start sniffing packets | Click the **Shark Fin** button |
| View file details | Statistics → Capture File Properties (file hash, capture time, comments, interface, statistics) |

---

## 🧭 Packet Navigation

| Action | How To |
|--------|--------|
| Go to a specific packet | Go → Go to Packet → insert number |
| Find packets | Edit → Find Packet → insert details |
| Mark/unmark packets | Select packet → Edit → Mark/Unmark Selected *(resets on new session)* |
| Add packet comment | Select packet → Edit → Packet Comment *(useful for investigation notes)* |
| Export selected packets | Select packets → File → Export Specified Packets |
| Export objects/files | File → Export Object → select protocol |
| Change time display format | View → Time Display Format |

---

## ⚠️ Expert Info

**Access:** Analyse → Expert Information

| Colour | Severity | Description |
|--------|----------|-------------|
| 🔵 Blue | Info | Normal workflow events |
| 🩵 Cyan | Notable | Application error codes and notable events |
| 🟡 Yellow | Warning | Unusual error codes |
| 🔴 Red | Problem | Malformed packets and critical issues |

---

## 🔍 Packet Filtering

| Filter Type | Description |
|-------------|-------------|
| **Capture Filter** | Captures only packets matching the filter — applied before capture starts |
| **Display Filter** | Shows only packets matching the filter — applied to already-captured traffic |

### Filtering Actions

| Action | How To |
|--------|--------|
| Filter by a field | Click the field → Analyse → Apply as Filter |
| Investigate all packets linked to an IP | Analyse → Conversation Filter |
| Highlight linked packets | View → Colourise Conversation |
| Add field as a column | Analyse → Apply as Column |
| View raw application-level traffic | Analyse → Follow TCP / UDP / HTTP Stream |
