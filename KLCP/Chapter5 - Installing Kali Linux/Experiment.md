# Kali Installation Analysis (My Actual VM)

**Environment**

```text
Hypervisor : VMware ESXi / vCenter
OS         : Kali Linux
Installation Type:
    Guided Partitioning
    → Use Entire Disk
    → Set Up Encrypted LVM

Disk Size  : ~40 GB
Filesystem : ext4
Encryption : LUKS
Storage    : LVM
```

---

# What I Chose During Installation

During installation I selected:

```text
Guided Partitioning
    ↓
Use Entire Disk
    ↓
Set Up Encrypted LVM
    ↓
Default Partition Layout
```

This corresponds to:

```mermaid
flowchart TD

A["Guided Partitioning"]

--> B["Use Entire Disk + Encrypted LVM"]

--> C["Create LUKS Encrypted Partition"]

--> D["Create LVM Inside Encryption"]

--> E["Create Root LV"]

--> F["Create Swap LV"]

```

---
![[Screenshot 2026-06-04 at 11.55.05 PM.png]]

![[Screenshot 2026-06-04 at 11.55.11 PM.png]]
# Actual Disk Layout

From:

```bash
lsblk -f
```

I got:

```text
sda
├─sda1     ext4
├─sda2
└─sda5     crypto_LUKS
    └─sda5_crypt
        ├─root
        └─swap
```

---

# Understanding Each Partition

## sda

```text
Entire Virtual Disk
≈ 40 GB
```

```mermaid
flowchart TD

A["sda (40 GB Virtual Disk)"]

--> B["sda1"]

--> C["sda2"]

--> D["sda5"]

```

---

# sda1

Mounted as:

```text
/boot
```

From:

```bash
mount | grep sda
```

Output:

```text
/dev/sda1 on /boot
```

---

Purpose:

```text
Stores:
Kernel
initrd
GRUB files
```

---

Why Is /boot Separate?

Because:

```text
LUKS Encryption
cannot be unlocked yet
during early boot
```

GRUB must first load:

```text
Kernel
Initramfs
```

before encryption can be unlocked.

Therefore:

```mermaid
flowchart LR

A["GRUB"]

--> B["/boot"]

--> C["Kernel"]

--> D["Unlock LUKS"]

```

---

# sda2

This is likely:

```text
Extended Partition
```

created because installer used:

```text
MBR Partition Table
```

instead of GPT.

Think:

```text
Container Partition
```

holding logical partitions.

---

# sda5

Filesystem:

```text
crypto_LUKS
```

This is the actual encrypted partition.

Output:

```text
sda5 crypto_LUKS
```

---

Everything important lives here.

```mermaid
flowchart TD

A["sda5"]

--> B["Encrypted With LUKS"]

--> C["Appears As sda5_crypt"]

```

---

# What Is sda5_crypt?

After entering passphrase:

```text
Encrypted Partition
↓
Unlocked
↓
Mapped Device
```

Linux creates:

```text
/dev/mapper/sda5_crypt
```

---

Think:

```text
Encrypted Safe
↓
Unlocked Safe
```

---

# LUKS Layer

```mermaid
flowchart TD

A["Physical Partition"]

--> B["LUKS Encryption"]

--> C["sda5_crypt"]

```

---

# LVM Layer

Inside the unlocked partition:

```bash
sudo pvs
```

Output:

```text
PV
/dev/mapper/sda5_crypt
```

This means:

```text
LVM uses
the decrypted partition
as a Physical Volume
```

---

Actual Flow:

```mermaid
flowchart TD

A["sda5"]

--> B["LUKS"]

--> C["sda5_crypt"]

--> D["LVM Physical Volume"]

```

---

# Volume Group (VG)

Output:

```bash
sudo vgs
```

```text
VG
kali-klcp-1-vg
```

---

Meaning:

```text
VG = Storage Pool
```

Think:

```text
Big Bucket Of Storage
```

---

```mermaid
flowchart TD

A["sda5_crypt"]

--> B["Volume Group"]

B["kali-klcp-1-vg"]

```

---

# Logical Volumes (LV)

Output:

```bash
sudo lvs
```

```text
root
swap_1
```

---

These are virtual partitions.

---

## Root LV

```text
root
≈ 37 GB
```

Mounted:

```text
/
```

Contains:

```text
Kali OS
Programs
Libraries
Tools
Users
```

---

## Swap LV

```text
swap_1
≈ 2 GB
```

Used as:

```text
Virtual Memory
```

when RAM fills up.

---

# Complete Storage Hierarchy

This is the most important diagram.

```mermaid
flowchart TD

A["sda (40 GB Disk)"]

--> B["sda1 (/boot)"]

--> C["sda2 (Extended Partition)"]

C --> D["sda5 (crypto_LUKS)"]

D --> E["sda5_crypt"]

E --> F["LVM Physical Volume"]

F --> G["Volume Group: kali-klcp-1-vg"]

G --> H["LV: root (37 GB)"]

G --> I["LV: swap_1 (2 GB)"]

H --> J["Mounted as /"]

I --> K["Mounted as [SWAP]"]

```

---

# Boot Process For This Installation

Since encryption is enabled:

Normal boot process changes.

---

## What Happens At Startup

```mermaid
flowchart LR

A["Power On"]

--> B["BIOS/UEFI"]

--> C["GRUB"]

--> D["Read Kernel From /boot"]

--> E["Kernel Starts"]

--> F["Ask For LUKS Passphrase"]

--> G["Unlock sda5"]

--> H["Activate LVM"]

--> I["Mount Root Filesystem"]

--> J["Start Kali"]

```

---

# Why /boot Is Not Encrypted

Question:

```text
Why isn't everything encrypted?
```

Because:

```text
GRUB must load
Kernel + Initramfs
before Linux starts
```

If kernel itself was encrypted:

```text
Chicken and Egg Problem
```

Need kernel to decrypt.

Need decryption to access kernel.

Therefore:

```text
/boot remains unencrypted
```

while everything else stays encrypted.

---

# What The Installer Built For Me

The installer automatically chose:

```text
/boot          ext4

LUKS Encryption
    ↓

LVM
    ↓

root
swap
```

This is the standard Kali encrypted installation.

Equivalent installer choice:

```text
Guided
→ Use Entire Disk
→ Set Up Encrypted LVM
→ Default Layout
```

---

# Mapping To Installation Concepts

|Installation Concept|Actual Device|
|---|---|
|Physical Disk|sda|
|Boot Partition|sda1|
|Encrypted Partition|sda5|
|LUKS Mapping|sda5_crypt|
|Physical Volume|sda5_crypt|
|Volume Group|kali-klcp-1-vg|
|Root Logical Volume|root|
|Swap Logical Volume|swap_1|
|Root Mount Point|/|
|Swap Mount Point|[SWAP]|

---

# One-Line Summary

```text
My Kali installation uses:

sda
 → /boot
 → LUKS Encryption
 → LVM
 → root filesystem
 → swap

This means the operating system and swap are encrypted,
while GRUB and the kernel remain accessible in /boot so the machine can start and later unlock the encrypted storage.
```

This is essentially the **reference architecture of a Guided "Entire Disk + Encrypted LVM" Kali installation**, and your screenshots confirm that the installer built it exactly as expected.