For Obsidian, I'd make it as a **reference tree** rather than a packet-flow diagram.

For Obsidian, I'd make it as a **reference tree** rather than a packet-flow diagram.

```
# iptables Command Syntax Reference

## Generic Syntax
```
iptables
    [-t table]
    COMMAND
    CHAIN
    CONDITIONS
    [-j TARGET]
    [TARGET_OPTIONS]
```
```

Example:

```bash
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

---

## Complete iptables Structure

```mermaid
flowchart TD

    A["iptables"]

    --> B["-t table"]

    --> C["COMMAND"]

    --> D["CHAIN"]

    --> E["CONDITIONS"]

    --> F["-j TARGET"]

    --> G["TARGET OPTIONS"]

    B --> B1["filter (default)"]
    B --> B2["nat"]
    B --> B3["mangle"]
    B --> B4["raw"]

    C --> C1["-L List"]
    C --> C2["-A Append"]
    C --> C3["-I Insert"]
    C --> C4["-D Delete"]
    C --> C5["-F Flush"]
    C --> C6["-P Policy"]
    C --> C7["-N New Chain"]
    C --> C8["-X Delete Chain"]

    D --> D1["INPUT"]
    D --> D2["OUTPUT"]
    D --> D3["FORWARD"]
    D --> D4["PREROUTING"]
    D --> D5["POSTROUTING"]
    D --> D6["Custom Chain"]

    E --> E1["-p protocol"]
    E --> E2["-s source"]
    E --> E3["-d destination"]
    E --> E4["-i interface"]
    E --> E5["-o interface"]
    E --> E6["--sport"]
    E --> E7["--dport"]
    E --> E8["--state"]

    E1 --> E11["tcp"]
    E1 --> E12["udp"]
    E1 --> E13["icmp"]
    E1 --> E14["icmpv6"]

    E8 --> E81["NEW"]
    E8 --> E82["ESTABLISHED"]
    E8 --> E83["RELATED"]
    E8 --> E84["INVALID"]

    F --> F1["ACCEPT"]
    F --> F2["DROP"]
    F --> F3["REJECT"]
    F --> F4["LOG"]
    F --> F5["RETURN"]
    F --> F6["DNAT"]
    F --> F7["SNAT"]
    F --> F8["MASQUERADE"]
    F --> F9["REDIRECT"]
    F --> F10["Custom Chain"]

    G --> G1["--to-source"]
    G --> G2["--to-destination"]
    G --> G3["--to-ports"]
    G --> G4["--log-prefix"]
    G --> G5["--reject-with"]
```

---

# COMMAND Reference

```mermaid
flowchart LR

A["COMMAND"]

--> B["-L"]

--> C["-A"]

--> D["-I"]

--> E["-D"]

--> F["-F"]

--> G["-P"]

--> H["-N"]

--> I["-X"]

B --> B1["List Rules"]

C --> C1["Append Rule"]

D --> D1["Insert Rule"]

E --> E1["Delete Rule"]

F --> F1["Flush Rules"]

G --> G1["Default Policy"]

H --> H1["Create Chain"]

I --> I1["Delete Chain"]
```

---

# TABLE Reference

```mermaid
flowchart LR

A["TABLES"]

--> B["filter"]

--> C["nat"]

--> D["mangle"]

--> E["raw"]

B --> B1["Filtering"]

C --> C1["Address Translation"]

D --> D1["Packet Modification"]

E --> E1["Before Conntrack"]
```

---

# CHAIN Reference

```mermaid
flowchart TD

A["CHAINS"]

--> B["INPUT"]

--> C["OUTPUT"]

--> D["FORWARD"]

--> E["PREROUTING"]

--> F["POSTROUTING"]

B --> B1["Traffic to Firewall"]

C --> C1["Traffic from Firewall"]

D --> D1["Transit Traffic"]

E --> E1["Before Routing"]

F --> F1["After Routing"]
```

---

# CONDITION Reference

```mermaid
flowchart TD

A["CONDITIONS"]

--> B["Protocol"]

--> C["Source"]

--> D["Destination"]

--> E["Interface"]

--> F["Ports"]

--> G["State"]

B --> B1["tcp"]
B --> B2["udp"]
B --> B3["icmp"]

C --> C1["-s IP"]
C --> C2["-s Subnet"]

D --> D1["-d IP"]
D --> D2["-d Subnet"]

E --> E1["-i Incoming"]
E --> E2["-o Outgoing"]

F --> F1["--sport"]
F --> F2["--dport"]

G --> G1["NEW"]
G --> G2["ESTABLISHED"]
G --> G3["RELATED"]
G --> G4["INVALID"]
```

---

# TARGET Reference

```mermaid
flowchart TD

A["TARGETS"]

--> B["ACCEPT"]

--> C["DROP"]

--> D["REJECT"]

--> E["LOG"]

--> F["RETURN"]

--> G["SNAT"]

--> H["DNAT"]

--> I["MASQUERADE"]

--> J["REDIRECT"]

--> K["Custom Chain"]
```

---

# NAT-Specific Syntax

```mermaid
flowchart TD

A["NAT"]

--> B["SNAT"]

--> C["DNAT"]

--> D["MASQUERADE"]

--> E["REDIRECT"]

B --> B1["--to-source"]

C --> C1["--to-destination"]

D --> D1["Interface IP"]

E --> E1["--to-ports"]
```

---

# Real Command Formula

```text
iptables
-t <table>
<command>
<chain>
<conditions>
-j <target>
<target-options>
```

Examples:

```bash
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

iptables -A INPUT -s 10.0.0.5 -j DROP

iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

iptables -t nat -A PREROUTING -p tcp --dport 80 \
-j DNAT --to-destination 192.168.1.100:80
```

This becomes a complete "iptables grammar tree" that you can keep beside your notes and use as a cheat sheet while learning.
