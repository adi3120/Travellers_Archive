# NAT Scenarios Using iptables

When using iptables, NAT rules are placed in the **nat table**.

```mermaid
flowchart LR

A["Packet"]

--> B["nat Table"]

--> C["PREROUTING"]
--> D["POSTROUTING"]
--> E["OUTPUT"]
```

---

# 1. Source NAT (SNAT)

## Scenario

Internal network:

```text
192.168.1.0/24
```

Public IP:

```text
203.0.113.10
```

All users going to the Internet should appear as:

```text
203.0.113.10
```

---

## Topology

```mermaid
flowchart LR

A["192.168.1.100"]

--> B["Linux Gateway"]

--> C["203.0.113.10"]

--> D["Internet"]
```

---

## iptables Rule

```bash
iptables -t nat \
-A POSTROUTING \
-s 192.168.1.0/24 \
-o eth0 \
-j SNAT \
--to-source 203.0.113.10
```

---

## Packet Translation

Before:

```text
SRC=192.168.1.100
DST=8.8.8.8
```

After:

```text
SRC=203.0.113.10
DST=8.8.8.8
```

---

# 2. Dynamic PAT (MASQUERADE)

Most common home-router NAT.

---

## Scenario

WAN IP changes via DHCP.

```text
ISP assigns public IP dynamically.
```

---

## Topology

```mermaid
flowchart LR

A["LAN Devices"]

--> B["Linux Router"]

--> C["Dynamic ISP IP"]

--> D["Internet"]
```

---

## iptables Rule

```bash
iptables -t nat \
-A POSTROUTING \
-o eth0 \
-j MASQUERADE
```

---

## Why MASQUERADE?

With SNAT:

```bash
--to-source 203.0.113.10
```

must be known.

With MASQUERADE:

```text
Use whatever IP exists on eth0
```

automatically.

---

# 3. Static NAT (1:1 NAT)

## Scenario

Internal Web Server:

```text
192.168.1.100
```

Public IP:

```text
203.0.113.100
```

Need permanent 1:1 mapping.

---

## Topology

```mermaid
flowchart LR

A["Internet"]

--> B["203.0.113.100"]

--> C["Firewall"]

--> D["192.168.1.100"]
```

---

## Inbound Translation

```bash
iptables -t nat \
-A PREROUTING \
-d 203.0.113.100 \
-j DNAT \
--to-destination 192.168.1.100
```

---

## Return Traffic

```bash
iptables -t nat \
-A POSTROUTING \
-s 192.168.1.100 \
-j SNAT \
--to-source 203.0.113.100
```

---

## Packet Flow

```mermaid
flowchart LR

A["203.0.113.100"]

--> B["DNAT"]

--> C["192.168.1.100"]

--> D["SNAT"]

--> E["203.0.113.100"]
```

---

# 4. Dynamic NAT (Pool NAT)

## Scenario

Internal users:

```text
192.168.1.0/24
```

Public Pool:

```text
203.0.113.10-203.0.113.20
```

---

### Important

Pure Dynamic NAT is easy on Cisco ASA/FTD.

Linux iptables does **not natively provide pool-based dynamic NAT as cleanly**.

Typically Linux uses:

- SNAT
    
- PAT
    
- MASQUERADE
    

instead.

For pool NAT, administrators often use:

```text
iproute2
nftables
multiple SNAT rules
```

---

Example:

```bash
iptables -t nat \
-A POSTROUTING \
-s 192.168.1.0/25 \
-j SNAT \
--to-source 203.0.113.10

iptables -t nat \
-A POSTROUTING \
-s 192.168.1.128/25 \
-j SNAT \
--to-source 203.0.113.11
```

---

# 5. Destination NAT (DNAT)

## Scenario

Public Web Server Access.

Internet user accesses:

```text
203.0.113.10
```

Actual server:

```text
192.168.1.100
```

---

## Rule

```bash
iptables -t nat \
-A PREROUTING \
-p tcp \
--dport 80 \
-j DNAT \
--to-destination 192.168.1.100:80
```

---

## Translation

Before:

```text
DST=203.0.113.10
```

After:

```text
DST=192.168.1.100
```

---

# 6. Port Forwarding (Special DNAT)

## Scenario

Expose SSH server.

Public:

```text
203.0.113.10:2222
```

Internal:

```text
192.168.1.100:22
```

---

## Rule

```bash
iptables -t nat \
-A PREROUTING \
-p tcp \
--dport 2222 \
-j DNAT \
--to-destination 192.168.1.100:22
```

---

## Flow

```mermaid
flowchart LR

A["203.0.113.10:2222"]

--> B["DNAT"]

--> C["192.168.1.100:22"]
```

---

# 7. Double NAT

## Scenario

Two Linux Routers.

```mermaid
flowchart LR

A["Client"]

--> B["Router-1"]

--> C["Router-2"]

--> D["Internet"]
```

---

Router-1:

```bash
iptables -t nat \
-A POSTROUTING \
-o eth0 \
-j MASQUERADE
```

---

Router-2:

```bash
iptables -t nat \
-A POSTROUTING \
-o eth0 \
-j MASQUERADE
```

---

Translation:

```text
192.168.1.10

↓

10.0.0.2

↓

203.0.113.10
```

---

# 8. Hairpin NAT (NAT Reflection)

Very common interview question.

---

## Scenario

Inside user accesses public server IP.

Server:

```text
192.168.1.100
```

Public NAT:

```text
203.0.113.10
```

User:

```text
192.168.1.50
```

tries:

```text
http://203.0.113.10
```

---

Without Hairpin NAT:

```text
Fails
```

---

Rule:

```bash
iptables -t nat \
-A PREROUTING \
-d 203.0.113.10 \
-j DNAT \
--to-destination 192.168.1.100
```

Plus matching SNAT rule.

---

# 9. Transparent Proxy (REDIRECT)

## Scenario

Force users through Squid.

---

Rule:

```bash
iptables -t nat \
-A PREROUTING \
-p tcp \
--dport 80 \
-j REDIRECT \
--to-port 3128
```

---

Flow

```mermaid
flowchart LR

A["HTTP Request"]

--> B["REDIRECT"]

--> C["Squid 3128"]
```

---

# 10. Cisco FTD Equivalent Mapping

|Cisco NAT|iptables|
|---|---|
|Dynamic PAT|MASQUERADE|
|Dynamic PAT to Interface|MASQUERADE|
|Static NAT|DNAT + SNAT|
|Dynamic NAT|SNAT Pool|
|Port Forward|DNAT|
|Twice NAT|Combined SNAT + DNAT|
|Identity NAT|No NAT Rule|
|Auto NAT|nat table rules|
|Manual NAT|nat table rules|

---

# The Most Important NAT Example (Linux Router)

```bash
# Enable routing
echo 1 > /proc/sys/net/ipv4/ip_forward

# Allow forwarding
iptables -A FORWARD -j ACCEPT

# PAT/NAT
iptables -t nat \
-A POSTROUTING \
-o eth0 \
-j MASQUERADE
```

This 3-line configuration is essentially what every home router does to let private hosts access the Internet.