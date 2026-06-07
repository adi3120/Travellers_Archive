# Netfilter, iptables, and Firewall Fundamentals

> Source: Kali Linux Documentation

---

# 1. What is a Firewall?

A **firewall** examines network packets entering or leaving a system and decides whether to:

- Allow them
    
- Reject them
    
- Drop them
    
- Modify them
    

```mermaid
flowchart LR
    A["Incoming Packet"] --> B["Firewall"]
    B --> C["Allow"]
    B --> D["Reject"]
    B --> E["Drop"]
    B --> F["Modify"]
```

## Types of Firewalls

### Network Firewall

Protects an entire network.

```mermaid
flowchart LR
    A["Internet"] --> B["Firewall Gateway"]
    B --> C["Internal Network"]
```

### Local Firewall

Protects a single machine.

```mermaid
flowchart LR
    A["Internet"] --> B["Linux Host"]
    B --> C["Local Firewall"]
```

---

# 2. Netfilter Overview

Linux implements firewall functionality through **Netfilter**.

Netfilter is built directly into the Linux kernel.

Users interact with Netfilter using:

|Tool|Purpose|
|---|---|
|`iptables`|IPv4 Firewall|
|`ip6tables`|IPv6 Firewall|
|`fwbuilder`|GUI Firewall Manager|

```mermaid
flowchart TD
    A["Administrator"] --> B["iptables"]
    A --> C["ip6tables"]
    A --> D["fwbuilder"]

    B --> E["Netfilter"]
    C --> E
    D --> E

    E --> F["Linux Kernel"]
```

---

# 3. Why Use Netfilter?

Netfilter can:

- Allow traffic
    
- Block traffic
    
- Log traffic
    
- Perform NAT
    
- Modify packets
    
- Redirect traffic
    

Examples:

- Allow SSH
    
- Block attackers
    
- Port forwarding
    
- Transparent proxy
    
- Internet sharing
    

---

# 4. Netfilter Tables

Netfilter organizes rules into **tables**.

Each table serves a different purpose.

---

## 4.1 Filter Table

Used for packet filtering decisions.

Common actions:

- ACCEPT
    
- REJECT
    
- DROP
    

```mermaid
flowchart LR
    A["Packet"] --> B["Filter Table"]
    B --> C["ACCEPT"]
    B --> D["REJECT"]
    B --> E["DROP"]
```

---

## 4.2 NAT Table

Used for Network Address Translation.

Functions:

- Source NAT
    
- Destination NAT
    
- Port Forwarding
    
- Masquerading
    

```mermaid
flowchart LR
    A["Private IP"] --> B["NAT Table"]
    B --> C["Public IP"]
```

---

## 4.3 Mangle Table

Used to modify packets.

Examples:

- Change ToS
    
- Modify packet headers
    
- Advanced packet manipulation
    

```mermaid
flowchart LR
    A["Packet"] --> B["Mangle Table"]
    B --> C["Modified Packet"]
```

---

## 4.4 Raw Table

Processes packets before connection tracking.

```mermaid
flowchart LR
    A["Packet"] --> B["Raw Table"]
    B --> C["Connection Tracking"]
```

---

# 5. Packet Flow Through Netfilter

A packet can pass through several chains depending on its destination.

```mermaid
flowchart TD
    A["Packet Arrives"]

    A --> B["PREROUTING"]

    B --> C{"Destination?"}

    C -->|"Firewall Itself"| D["INPUT"]

    C -->|"Forwarded"| E["FORWARD"]

    D --> F["Local Process"]

    F --> G["OUTPUT"]

    E --> H["POSTROUTING"]

    G --> H

    H --> I["Packet Leaves"]
```

---

# 6. IPv4 vs IPv6

|Command|Protocol|
|---|---|
|`iptables`|IPv4|
|`ip6tables`|IPv6|

Both must usually be configured.

```mermaid
flowchart LR
    A["Firewall Rules"]

    A --> B["iptables"]
    A --> C["ip6tables"]

    B --> D["IPv4 Traffic"]
    C --> E["IPv6 Traffic"]
```

---

# 7. Core Terminology

|Term|Meaning|
|---|---|
|Packet|Unit of network communication|
|Rule|Condition + Action|
|Chain|Ordered list of rules|
|Table|Collection of chains|
|Target|Action performed|
|Policy|Default action if no rule matches|

---

# 8. Rule Processing Logic

Netfilter evaluates rules sequentially.

```mermaid
flowchart TD

    A["Packet"]

    A --> B["Rule 1"]

    B -->|"Match"| C["Execute Action"]

    B -->|"No Match"| D["Rule 2"]

    D -->|"Match"| E["Execute Action"]

    D -->|"No Match"| F["Rule 3"]

    F -->|"No Match"| G["Default Policy"]
```

### Important

Rules are processed:

```text
Top → Bottom
```

First matching rule generally determines packet fate.

---

# 9. Default Security Concerns

Kali disables most network services by default.

When enabling services:

### Risks

- No firewall configured by default
    
- Service may listen on all interfaces
    
- Default credentials may exist
    
- Many services run as root
    

```mermaid
flowchart LR
    A["Enable Service"]

    A --> B["Publicly Reachable"]

    B --> C["Weak Password"]
    B --> D["Default Credentials"]
    B --> E["Root Privileges"]

    C --> F["Compromise"]
    D --> F
    E --> F
```

---

# 10. SSH Warning

Before enabling SSH:

1. Change default password.
    
2. Generate new SSH host keys if needed.
    
3. Restrict authentication methods.
    

```mermaid
flowchart TD
    A["Enable SSH"]

    A --> B["Change Password"]

    B --> C["Generate New Host Keys"]

    C --> D["Secure Authentication"]

    D --> E["Enable Service"]
```

---

# Quick Summary

```mermaid
mindmap
  root["Netfilter"]
    "iptables"
    "ip6tables"
    "Tables"
      "Filter"
      "NAT"
      "Mangle"
      "Raw"
    "Chains"
      "INPUT"
      "OUTPUT"
      "FORWARD"
    "Rules"
      "Conditions"
      "Actions"
    "Packet Filtering"
    "NAT"
    "Logging"
    "Security"
```

---

# Next Section

**2. Chains, Rules, Conditions, Targets, and iptables Syntax**

We'll cover:

- INPUT / OUTPUT / FORWARD
    
- PREROUTING / POSTROUTING
    
- Rule structure
    
- Conditions (`-s`, `-d`, `-p`, `-i`, `-o`)
    
- Actions (`ACCEPT`, `DROP`, `REJECT`, `LOG`)
    
- Connection states (`NEW`, `ESTABLISHED`, `RELATED`)
    
- Packet traversal examples
    
- Real packet flow diagrams
    
- How iptables actually evaluates rules internally.