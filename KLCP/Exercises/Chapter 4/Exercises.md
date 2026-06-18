# 4.4.1 Getting Set Up, Downloading, Verifying, and Burning Kali

## Exercise

### Task 1

Reuse the **Kali Live 64-bit ISO** from the previous chapter.

### Screenshot

> ![[Pasted image 20260616202809.png]]

---

### Task 2

Download a pre-made **Kali Linux VM release** (64-bit version recommended).

### Screenshot

> ![[Pasted image 20260616202947.png]]
> ![[Pasted image 20260616204224.png]]
![[Pasted image 20260616204956.png]]
---

### Task 3

Launch the Kali VM.

### Screenshot

> Attach screenshot here

---

### Task 4

Log in using:

```text
Username: kali
Password: kali
```

### Screenshot

> Attach screenshot here

---

### Task 5

Copy the Kali Live ISO into the Kali VM.

### Screenshot

> Attach screenshot here

---

### Task 6

Download and import Kali's public GPG key.

The PGP(Pretty Good Privacy)/GPG(GNU Privacy Guard) security model is very unique. Anyone can generate any key with any identity, but you would only trust that key if it has been signed by another key that you already trust. When you sign a key, you certify that you met the holder of the key and that you know that the associated identity is correct. And you define the initial set of keys that you trust, which obviously includes your own key.
### Screenshot

> Attach screenshot here

---

### Task 7

Extract the GPG fingerprint.

### Screenshot

> Attach screenshot here

---

### Task 8

Download:

- SHA256SUMS
    
- SHA256SUMS.gpg
    

### Screenshot

> Attach screenshot here

---

### Task 9

Verify the authenticity of the SHA256SUMS file.

### Screenshot

> Attach screenshot here

---

### Task 10

Generate the SHA256 checksum of the downloaded ISO.

### Screenshot

> Attach screenshot here

---

### Task 11

Compare the ISO checksum with the value listed in SHA256SUMS.

### Screenshot

> Attach screenshot here

---

### Task 12

Insert a USB drive and determine its device identifier.

### Screenshot

> Attach screenshot here

---

### Task 13

Create a bootable Kali USB drive using the ISO image.

### Screenshot

> Attach screenshot here

---

# Questions

## Question 1

What are some good use cases for booting Kali Live?

### Answer Space

---

---

---

---

## Question 2

What are some bad use cases for booting Kali Live?

### Answer Space

---

---

---

---

## Question 3

Why can a Kali ISO be written directly to a USB drive using `dd` and still remain bootable?

### Answer Space

---

---

---

---

# 4.4.2 Booting Kali

## Exercise

### Task 1

Boot from the Kali USB drive created in the previous exercise.

Select **Live Mode**.

### Screenshot

> Attach screenshot here

---

### Task 2

Create a 6 GB test image inside:

```text
/home/kali
```

### Screenshot

>![[Pasted image 20260616210901.png]]

---

### Task 3

Observe and record what happens.

### Screenshot

> Attach screenshot here

---

### Task 4

Explain why this behavior occurs.

### Answer Space

---

---

---

---

### Task 5

Reboot the system.

### Screenshot

> Attach screenshot here

---

### Task 6

Verify whether the changes persisted after rebooting.

### Screenshot

> Attach screenshot here

---

# 4.4.3 Editing Boot Parameters

## Exercise

### Task 1

Boot a virtual machine directly from the Kali ISO.

Ensure the network adapter is configured in **NAT mode**.

### Screenshot

> Attach screenshot here

---

### Task 2

At the boot menu, edit the Live boot entry.

Remove the following boot parameter:

```text
quiet
```

### Screenshot

> Attach screenshot here

---

### Task 3

Boot the system using the modified boot parameters.

### Screenshot

> Attach screenshot here

---

### Task 4

Observe the boot process.

Did removing the `quiet` parameter increase boot verbosity?

### Screenshot

> Attach screenshot here

---

### Task 5

Compare the boot parameters used in:

- Live Mode
    
- Forensics Mode
    

### Screenshot

> Attach screenshot here

---

### Task 6

Identify the differences between the two boot modes.

### Answer Space

---

---

---

---

# Solutions

## 4.4.1 Getting Set Up, Downloading, Verifying, and Burning Kali

Download ISO:

```bash
wget https://archive.kali.org/kali-images/kali-2020.3/kali-linux-2020.3-live-amd64.iso
```

Import Kali GPG key:

```bash
wget -q -O - https://archive.kali.org/archive-key.asc | gpg --import
```

or

```bash
gpg --keyserver hkps://keys.openpgp.org --recv-key 44C6513A8E4FB3D30875F758ED444FF07D8D0BF6
```

View fingerprint:

```bash
gpg --fingerprint 44C6513A8E4FB3D30875F758ED444FF07D8D0BF6
```

Download checksum files:

```bash
wget https://archive.kali.org/kali-images/kali-2020.3/SHA256SUMS
```

```bash
wget https://archive.kali.org/kali-images/kali-2020.3/SHA256SUMS.gpg
```

Verify signature:

```bash
gpg --verify SHA256SUMS.gpg SHA256SUMS
```

Expected output:

```text
Good signature from "Kali Linux Repository (devel@kali.org)"
```

Generate ISO hash:

```bash
shasum -a 256 kali-linux-2020.3-live-amd64.iso
```

Compare against SHA256SUMS:

```bash
grep kali-linux-2020.3-live-amd64 SHA256SUMS
```

Identify USB device:

```bash
dmesg
```

Create bootable USB:

```bash
sudo su
```

```bash
dd if=kali-linux-2020.3-live-amd64.iso of=/dev/sdb bs=1M
```

---

### Question Answers

#### Q1. Good use cases for Kali Live

- Carrying a portable Kali installation
    
- Testing Kali without modifying a computer
    
- Digital forensics investigations
    
- Temporary penetration-testing environment
    
- Troubleshooting systems
    

---

#### Q2. Bad use cases for Kali Live

- Permanent daily-use installation
    
- Long-term storage of files
    
- Systems requiring persistence
    
- Machines with limited RAM
    

---

#### Q3. Why does `dd` work directly on the ISO?

Because Kali ISOs are **isohybrid** images.

The `isohybrid` utility adds a partition table to the ISO while keeping it a valid ISO image, allowing it to boot directly from optical media or USB storage.

---

# 4.4.2 Booting Kali

Boot from the USB and select:

```text
Live System
```

Create a 6 GB file:

```bash
sudo dd if=/dev/zero of=test.img bs=4M count=6144
```

### What happened?

The command eventually reports:

```text
No space left on device
```

### Why?

In Live Mode, filesystem changes are stored in RAM.

The writable filesystem exists in memory only. Once RAM is exhausted, no additional data can be written.

### Persistence Check

After rebooting:

- The file `test.img` is gone.
    
- Any other changes are lost.
    

This confirms that Live Mode is non-persistent.

---

# 4.4.3 Editing Boot Parameters

Boot the VM directly from the ISO.

Enable NAT networking.

At the boot menu:

1. Highlight **Live System**
    
2. Press **Tab**
    
3. Remove:
    

```text
quiet
```

4. Press **Enter**
    

### Result

The system displays significantly more boot messages during startup because output is no longer suppressed.

### Differences Between Live and Forensics Modes

Forensics mode includes additional parameters:

```text
noswap
```

```text
noautomount
```

### Purpose

|Parameter|Purpose|
|---|---|
|`noswap`|Prevents use of swap partitions|
|`noautomount`|Prevents automatic mounting of disks|

These options help preserve evidence integrity during forensic investigations.