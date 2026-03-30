# TCPDump

## 🖥️ Overview

TCPDump is a **command-line packet monitor** used to capture, inspect and monitor network traffic in real time. It can filter traffic to inspect specific protocols or display specific packets.

---

## ⚙️ Arguments

### Core Arguments

| Argument | Description | Example |
|----------|-------------|---------|
| `-i interfaceName` | Listen on a specific interface | `tcpdump -i eth0` |
| `-i any` | Listen on all interfaces | `tcpdump -i any` |
| `ip a s` | List available network interfaces | `ip a s` |
| `-w fileName` | Save captured packets to a `.pcap` file (for Wireshark) | `tcpdump -w capture.pcap` |
| `-r fileName` | Read from a packet capture file | `tcpdump -r capture.pcap` |
| `-c count` | Capture a specific number of packets (otherwise runs until `Ctrl+C`) | `tcpdump -c 50` |
| `-n` | Disable DNS lookup (don't resolve IP/domain names) | `tcpdump -n` |
| `-nn` | Disable both DNS lookup and port number resolution | `tcpdump -nn` |
| `-v` | Verbose output — includes TTL, ID, total length, options | `tcpdump -v` |
| `-vv` | More verbose | `tcpdump -vv` |
| `-vvv` | Even more verbose | `tcpdump -vvv` |
| `wc -l` | Count number of lines in output | `tcpdump ... \| wc -l` |

### Display Arguments

| Argument | Description |
|----------|-------------|
| `-q` | Quick/brief output |
| `-e` | Print link-level header including MAC address |
| `-A` | Show packet data in ASCII |
| `-xx` | Show packet data in hexadecimal |
| `-X` | Show packet headers and data in both hex and ASCII |

---

## 🔍 Filtering

> **Note:** Always run as root (`sudo`) when capturing live traffic.

### Basic Filters

| Filter | Description | Example |
|--------|-------------|---------|
| `host` | Filter by host IP or hostname | `sudo tcpdump host google.com -w http.pcap` |
| `port` | Filter by port number | `sudo tcpdump -i ens5 port 53 -n` |
| `src port` | Filter by source port | `tcpdump src port 443` |
| `dst port` | Filter by destination port | `tcpdump dst port 80` |
| Protocol | Filter by protocol — `icmp`, `ip`, `ip6`, `udp`, `tcp` | `sudo tcpdump -i ens5 icmp -n` |

### Advanced Filters

| Filter | Purpose | Example |
|--------|---------|---------|
| `greater N` | Show packets >= N bytes | `tcpdump -n -r traffic.pcap greater 15000` |
| `less N` | Show packets <= N bytes | `tcpdump -n -r traffic.pcap less 500` |
| `ether[0] & 1 != 0` | Show packets sent to a **multicast** address — takes the first byte of the ethernet header and applies a bitwise AND with `1` (binary `0000 0001`), returning true if the result is not zero | `tcpdump -n "ether[0] & 1 != 0"` |
| `ip[0] & 0xf != 5` | Catch all **IP packets with options** — compares first byte of IP header against hex `F` (binary `0000 1111`), returning true if not equal to `5` (binary `0000 0101`) | `tcpdump -n "ip[0] & 0xf != 5"` |

### TCP Flags

Available flags: `tcp-syn` / `tcp-ack` / `tcp-fin` / `tcp-rst` / `tcp-push`

| Filter | Description | Example |
|--------|-------------|---------|
| `tcp[tcpflags] == tcp-syn` | Capture packets with **only** the SYN flag set | `tcpdump "tcp[tcpflags] == tcp-syn"` |
| `tcp[tcpflags] & tcp-syn != 0` | Capture packets with **at least** the SYN flag set | `tcpdump "tcp[tcpflags] & tcp-syn != 0"` |
| `tcp[tcpflags] & (tcp-syn\|tcp-ack) != 0` | Capture packets with **at least** SYN or ACK set | `tcpdump "tcp[tcpflags] & (tcp-syn\|tcp-ack) != 0"` |

---

## 📋 Command Examples

### Arguments

```bash
# Capture 50 packets on eth0 with verbose output
tcpdump -i eth0 -c 50 -v

# Capture on wifi interface and save to file (runs until Ctrl+C)
tcpdump -i wlo1 -w data.pcap

# Capture on all interfaces without DNS or port resolution
tcpdump -i any -nn
```

### Filtering

```bash
# Capture TCP packets on port 22 (SSH) across all interfaces
tcpdump -i any tcp port 22

# Capture UDP on port 123 (NTP) on the wifi interface
tcpdump -i wlo1 udp port 123

# Capture HTTPS traffic to/from example.com on eth0
tcpdump -i eth0 host example.com and tcp port 443

# Show ARP request for a specific IP (display as IP, not hostname)
tcpdump -n -r traffic.pcap arp dst 192.168.124.137
```
