# 5.2.2 Installation on a Fully Encrypted File System

> Full Disk Encryption protects your data if your laptop, SSD, or hard drive is lost or stolen. Without the encryption passphrase, the data remains unreadable.

---

# Why Encrypt a Disk?

Without encryption:

```mermaid
flowchart LR

A["Laptop Stolen"]

--> B["Remove SSD"]

--> C["Read Files"]

```

Attacker can access:

- Documents
    
- Password databases
    
- Browser data
    
- SSH keys
    
- Company data
    

---

With encryption:

```mermaid
flowchart LR

A["Laptop Stolen"]

--> B["Remove SSD"]

--> C["Encrypted Data"]

--> D["Cannot Read"]

```

Without the passphrase:

```text
Data = Useless
```

---

# Technologies Used

Encrypted Kali installation combines:

```mermaid
flowchart TD

A["LUKS"]

--> B["Encryption"]

C["LVM"]

--> D["Storage Management"]

B --> E["Encrypted Logical Volumes"]

D --> E

```

Components:

|Technology|Purpose|
|---|---|
|LUKS|Encrypt data|
|LVM|Manage storage|
|ext4|Filesystem|

---

# LVM Refresher

LVM introduces another layer between partitions and filesystems.

Normal setup:

```mermaid
flowchart LR

A["Disk"]

--> B["Partition"]

--> C["Filesystem"]

```

LVM setup:

```mermaid
flowchart LR

A["Disk"]

--> B["Physical Volume"]

--> C["Volume Group"]

--> D["Logical Volume"]

--> E["Filesystem"]

```

---

## LVM Terminology

### Physical Volume (PV)

Actual storage.

Examples:

```text
/dev/sda3
/dev/nvme0n1p3
```

---

### Volume Group (VG)

Pool of storage.

Think:

```text
Storage Container
```

---

### Logical Volume (LV)

Virtual partition created from the storage pool.

Examples:

```text
root
home
swap
```

---

# Why Use LVM?

Without LVM:

```mermaid
flowchart TD

A["Disk 1"]

--> B["100 GB Partition"]

```

Partition size is fixed.

---

With LVM:

```mermaid
flowchart TD

A["Disk 1"]

--> B["Volume Group"]

C["Disk 2"]

--> B

B --> D["Root LV"]

B --> E["Home LV"]

```

Benefits:

- Combine multiple disks
    
- Resize volumes
    
- Expand storage later
    

---

# LUKS (Linux Unified Key Setup)

LUKS provides encryption.

Without LUKS:

```mermaid
flowchart LR

A["Filesystem"]

--> B["Disk"]

```

Data stored as plain text.

---

With LUKS:

```mermaid
flowchart LR

A["Filesystem"]

--> B["LUKS Encryption"]

--> C["Disk"]

```

Data stored in encrypted form.

---

# Encryption Layer Visualization

Normal disk:

```text
Files
↓
Filesystem
↓
Disk
```

Encrypted disk:

```text
Files
↓
Filesystem
↓
LUKS
↓
Disk
```

LUKS encrypts everything before it reaches disk.

---

# Why LVM + LUKS Together?

The installer creates:

```mermaid
flowchart TD

A["Physical Partition"]

--> B["LUKS Encryption"]

--> C["LVM Physical Volume"]

--> D["Volume Group"]

D --> E["Root"]

D --> F["Home"]

D --> G["Swap"]

```

Benefits:

- One passphrase
    
- Multiple logical volumes
    
- Entire system protected
    

---

# Encrypted Swap Partition

This is a commonly overlooked security issue.

---

## Problem

Encryption key lives in RAM.

```mermaid
flowchart LR

A["Encryption Key"]

--> B["RAM"]

```

During hibernation:

```mermaid
flowchart LR

A["RAM"]

--> B["Swap"]

```

The key may be written to disk.

---

If swap is not encrypted:

```mermaid
flowchart LR

A["Attacker"]

--> B["Reads Swap"]

--> C["Finds Encryption Key"]

```

Potentially compromising encrypted data.

---

## Solution

Encrypt swap too.

```mermaid
flowchart LR

A["RAM"]

--> B["Encrypted Swap"]

```

Installer warns if:

```text
Encrypted Disk
+
Unencrypted Swap
```

is detected.

---

# Guided Encrypted Installation

Select:

```text
Guided
→ Use Entire Disk
→ Set Up Encrypted LVM
```

```mermaid
flowchart TD

A["Guided Installation"]

--> B["Encrypted LVM"]

--> C["Enter Passphrase"]

--> D["Create Volumes"]

```

---

# Random Data Initialization

Installer fills encrypted partition with random data.

Why?

Without randomization:

```text
Used Blocks = Obvious
Unused Blocks = Obvious
```

Attacker learns information.

---

With randomization:

```text
Everything Looks Random
```

```mermaid
flowchart TD

A["Encrypted Partition"]

--> B["Random Data Everywhere"]

```

Makes analysis more difficult.

---

# Encryption Passphrase

Installer asks for:

```text
Encryption Passphrase
```

Every boot:

```mermaid
flowchart TD

A["Power On"]

--> B["Enter Passphrase"]

--> C["Unlock Disk"]

--> D["Boot Kali"]

```

Without passphrase:

```text
No Access
```

---

# What Happens During Boot?

Normal system:

```mermaid
flowchart LR

A["BIOS/UEFI"]

--> B["GRUB"]

--> C["Kernel"]

--> D["Kali"]

```

Encrypted system:

```mermaid
flowchart LR

A["BIOS/UEFI"]

--> B["GRUB"]

--> C["Enter Passphrase"]

--> D["Unlock LUKS"]

--> E["Kernel"]

--> F["Kali"]

```

---

# Partition Layout After Encryption

Typical result:

```mermaid
flowchart TD

A["Disk"]

--> B["Encrypted Partition"]

B --> C["LVM"]

C --> D["Root"]

C --> E["Home"]

C --> F["Swap"]

```

Everything inside the encrypted container is protected.

---

# Advantages

✅ Protects stolen laptops

✅ Protects removable drives

✅ Protects sensitive files

✅ Protects swap

✅ Transparent after login

---

# Disadvantages

❌ Must enter passphrase at boot

❌ Slight performance overhead

❌ Forgotten passphrase = data loss

```text
No Backdoor
No Recovery
No Passphrase = No Data
```

---

# Exam / Interview Notes

|Component|Purpose|
|---|---|
|LUKS|Disk encryption|
|LVM|Flexible storage management|
|PV|Physical Volume|
|VG|Volume Group|
|LV|Logical Volume|
|Swap|Disk-based overflow RAM|
|Encrypted Swap|Protects keys/data written from RAM|
|Passphrase|Unlocks encrypted storage|

---

# Quick Memory Diagram

```mermaid
flowchart TD

A["Disk"]

--> B["LUKS"]

--> C["LVM"]

C --> D["Root"]

C --> E["Home"]

C --> F["Swap"]

```

### Remember

```text
LUKS = Security
LVM  = Flexibility
```

Together they provide:

```text
Secure + Flexible Storage
```