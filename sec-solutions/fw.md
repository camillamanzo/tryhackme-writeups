# Firewalls

## 🔥 Overview

A firewall filters network traffic based on rules — blocking harmful traffic while allowing legitimate traffic through. Different firewall types operate at different OSI model layers and offer different levels of inspection.

---

## 🧱 Firewall Types

| Type | OSI Layers | Description |
|------|-----------|-------------|
| **Stateless** | Layer 3–4 | Filters packets based on predetermined rules only — no memory of previous connections. Fast but limited |
| **Stateful** | Layer 3–4 | Tracks connection state in a state table — uses connection history to make smarter decisions |
| **Proxy (Application-Level Gateway)** | Layer 7 | Acts as intermediary — inspects packet *contents*, masks internal IPs, supports content filtering |
| **NGFW (Next-Generation Firewall)** | Layer 3–7 | Deep packet inspection, intrusion prevention, heuristic analysis, SSL/TLS decryption, threat intelligence |

### Stateless vs Stateful — Key Difference

| Behaviour | Stateless | Stateful |
|-----------|-----------|---------|
| Tracks previous connections | ❌ No | ✅ Yes (state table) |
| Treats every packet as new | ✅ Yes | ❌ No |
| Can apply complex policies | ❌ Limited | ✅ Yes |
| Processing speed | ✅ Faster | Slightly slower |

### Firewall Characteristics Summary

| Firewall | Key Characteristics |
|----------|-------------------|
| **Stateless** | Basic filtering / No connection tracking / Efficient for high-speed networks |
| **Stateful** | Pattern recognition / Complex rules / Monitors active connections |
| **Proxy** | Inspects packet content / Content filtering / Application control / SSL/TLS decryption |
| **NGFW** | Advanced threat protection / IPS / Heuristic anomaly detection / SSL/TLS decryption / Threat intel feeds |

---

## 📐 Firewall Rules

Firewalls filter traffic based on built-in and custom rules.

### Rule Components

| Component | Description | Example |
|-----------|-------------|---------|
| **Source Address** | Origin IP of the traffic | `192.168.1.10` |
| **Destination Address** | Receiving IP of the traffic | `10.0.0.5` |
| **Port** | Port number used | `443`, `22`, `80` |
| **Protocol** | Protocol used in the communication | `TCP`, `UDP`, `ICMP` |
| **Action** | What to do when traffic matches the rule | `Allow`, `Deny`, `Forward` |
| **Direction** | Whether rule applies to inbound or outbound traffic | `Inbound`, `Outbound` |

### Actions

| Action | Description |
|--------|-------------|
| **Allow** | Traffic is permitted through |
| **Deny** | Traffic is blocked |
| **Forward** | Traffic is redirected to a different network segment |

### Directionality

| Direction | Applies To |
|-----------|-----------|
| **Inbound** | Incoming traffic only |
| **Outbound** | Outgoing traffic only |
| **Forward** | Traffic passing through to another network segment |

---

## 🪟 Windows Defender Firewall

Built-in Windows firewall — supports allowing/blocking programs and creating custom rules.

### Network Profiles

Windows uses **Network Location Awareness (NLA)** to detect the current network type and automatically apply the appropriate firewall profile.

| Profile | When Applied |
|---------|-------------|
| **Private** | Connected to a trusted home or work network |
| **Guest / Public** | Connected to an untrusted public network |
| **Domain** | Connected to a domain network (enterprise environments) |

> To allow or block apps per profile: **Windows Defender Firewall → Allow an app or feature through Windows Defender Firewall**

### Creating Custom Rules

```
1. Windows Defender Firewall → Advanced Settings
2. Select rule type: Inbound Rules or Outbound Rules
3. New Rule
4. Choose: Custom → Next
5. Configure: program, protocol, ports, scope, action, profile
6. Name and save the rule
```

---

## 🐧 Linux Firewall — iptables & UFW

### Netfilter

**Netfilter** is the framework built into the Linux kernel that enables packet filtering, NAT, and connection tracking. Both `iptables` and `ufw` are frontends to Netfilter.

### UFW — Uncomplicated Firewall

UFW provides a simplified command-line interface for managing firewall rules on Linux.

```bash
# Check firewall status
sudo ufw status

# Enable firewall
sudo ufw enable

# Allow all outgoing traffic by default
sudo ufw default allow outgoing

# Deny SSH incoming (port 22 TCP)
sudo ufw deny 22/tcp

# List all active rules with numbers
sudo ufw status numbered

# Delete a specific rule by number
sudo ufw delete 2
```

### UFW Command Reference

| Command | Description |
|---------|-------------|
| `sudo ufw status` | Show firewall status (active/inactive) and current rules |
| `sudo ufw enable` | Enable the firewall |
| `sudo ufw disable` | Disable the firewall |
| `sudo ufw default allow outgoing` | Allow all outgoing traffic by default |
| `sudo ufw default deny incoming` | Deny all incoming traffic by default |
| `sudo ufw allow 80/tcp` | Allow HTTP traffic |
| `sudo ufw allow 443/tcp` | Allow HTTPS traffic |
| `sudo ufw deny 22/tcp` | Deny SSH incoming |
| `sudo ufw status numbered` | List active rules with line numbers |
| `sudo ufw delete <n>` | Delete rule by number |
