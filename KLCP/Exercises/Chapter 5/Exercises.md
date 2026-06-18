
# 5.7.1 Kali Linux Full Disk Encryption Install

## Exercise

### Task 1

Determine the minimum required resources for a Kali Linux VM.

### Screenshot


### System Requirements

https://www.kali.org/docs/installation/hard-disk-install/#system-requirements

The installation requirements for Kali Linux will vary depending on what you would like to install and your setup. For system requirements:

- On the low end, you can set up Kali Linux as a basic Secure Shell (SSH) server with no desktop, using **as little as 128 MB of RAM (512 MB recommended)** and **2 GB of disk space**.
- On the higher end, if you opt to install the default Xfce4 desktop and the `kali-linux-default` [metapackage](https://www.kali.org/docs/general-use/metapackages/), you should really aim for **at least 2 GB of RAM** and **20 GB of disk space**.
    - When using resource-intensive applications, such as Burp Suite, [they recommend](https://portswigger.net/support/burp-suite-software-faqs) at least **8 GB of RAM** _(and even more if it is a large web application!)_ or using simultaneous programs at the same time.

[](https://www.kali.org/docs/installation/hard-disk-install/#installation-prerequisites)

---

### Task 2

Create a new virtual machine.

### Screenshot

> Attach screenshot here

---

### Task 3

Boot the VM using the Kali ISO installer.

### Screenshot

> Attach screenshot here

---

### Task 4

Perform a standard Kali installation using **Full Disk Encryption**.

### Screenshot

> Attach screenshot here

---

### Task 5

Complete the installation and boot into Kali Linux.

### Screenshot

> Attach screenshot here

---

### Task 6

Verify that the VM network adapter is configured in **NAT mode**.

### Screenshot

> Attach screenshot here

---

### Task 7

Identify the technologies used for disk encryption.

### Screenshot

> LUKS

---

# Questions

## Question 1

What are the minimum hardware requirements for a Kali VM?

### Answer Space

---

---

---

## Question 2

What technologies are used in Kali's encrypted installation?

### Answer Space

---

---

---

# 5.7.2 Kali Linux Unattended Install

## Exercise

### Task 1

Create a new VM with at least the recommended hardware requirements.

### Screenshot

> Attach screenshot here

---

### Task 2

Configure an unattended installation using a preseed file hosted over HTTP or HTTPS.

### Screenshot

> ![[Pasted image 20260617165843.png]]

---

### Task 3

Configure the following parameters:

- Locale
    
- Keymap
    
- Hostname
    
- Domain
    

### Screenshot

> Attach screenshot here

---

### Task 4

Modify the boot parameters to load the preseed file.

### Screenshot

> Attach screenshot here

---

### Task 5

Start the installation and verify that no manual interaction is required.

### Screenshot

> Attach screenshot here

---

### Task 6

Complete the unattended installation.

### Screenshot

> Attach screenshot here

---

# Question

## Question 1

Why can't a network-hosted preseed file always handle locale, keymap, hostname, and domain settings?

### Answer Space

---

---

---

---

# 5.7.3 Kali Linux Standard ARM Install

## Exercise

### Task 1

Download the appropriate ARM image for your device.

### Screenshot

> Attach screenshot here

---

### Task 2

Enable and start the SSH service.

### Screenshot

> Attach screenshot here

---

### Task 3

Transfer the ARM image to the Kali system using SCP.

### Screenshot

> Attach screenshot here

---

### Task 4

Insert the SD card and determine its device identifier.

### Screenshot

> Attach screenshot here

---

### Task 5

Extract the ARM image.

### Screenshot

> Attach screenshot here

---

### Task 6

Write the image to the SD card.

### Screenshot

> Attach screenshot here

---

### Task 7

Boot the ARM device using the prepared SD card.

### Screenshot

> Attach screenshot here

---

# 5.7.4 Custom Kali Linux ARM Install

## Exercise

### Task 1

Create a working directory for the ARM build environment.

### Screenshot

> Attach screenshot here

---

### Task 2

Download the ARM cross-compiler.

### Screenshot

> Attach screenshot here

---

### Task 3

Configure the PATH environment variable.

### Screenshot

> Attach screenshot here

---

### Task 4

Download the Kali ARM build scripts.

### Screenshot

> Attach screenshot here

---

### Task 5

Install all required build dependencies.

### Screenshot

> Attach screenshot here

---

### Task 6

Edit the Raspberry Pi build script.

### Screenshot

> Attach screenshot here

---

### Task 7

Modify the build script to:

- Remove the desktop environment
    
- Reduce installed packages
    
- Configure a static IP address
    
- Enable SSH at boot
    

### Screenshot

> Attach screenshot here

---

### Task 8

Build the custom ARM image.

### Screenshot

> Attach screenshot here

---

### Task 9

Verify the generated image files.

### Screenshot

> Attach screenshot here

---

### Task 10

Write the generated image to an SD card.

### Screenshot

> Attach screenshot here

---

### Task 11

Boot the Raspberry Pi and verify SSH connectivity.

### Screenshot

> Attach screenshot here

---

# 5.7.5 Kali Linux ARM Chroot

## Exercise

### Task 1

Install QEMU and ARM emulation packages.

### Screenshot

> Attach screenshot here

---

### Task 2

Create a mount point for the SD card.

### Screenshot

> Attach screenshot here

---

### Task 3

Mount the Raspberry Pi root filesystem.

### Screenshot

> Attach screenshot here

---

### Task 4

Mount the required virtual filesystems.

### Screenshot

> Attach screenshot here

---

### Task 5

Copy the ARM QEMU binary into the mounted filesystem.

### Screenshot

> Attach screenshot here

---

### Task 6

Enter the ARM filesystem using chroot.

### Screenshot

> Attach screenshot here

---

### Task 7

Install additional packages:

- mlocate
    
- net-tools
    
- hostapd
    
- dnsmasq
    

### Screenshot

> Attach screenshot here

---

### Task 8

Exit the chroot environment.

### Screenshot

> Attach screenshot here

---

### Task 9

Unmount all mounted filesystems.

### Screenshot

> Attach screenshot here

---

### Task 10

Insert the SD card into the Raspberry Pi and boot the device.

### Screenshot

> Attach screenshot here

---

# Solutions

## 5.7.1 Full Disk Encryption Install

### Minimum Requirements

- 2 GB RAM
    
- 20 GB Disk Space
    

### Encryption Technologies

- LUKS (Linux Unified Key Setup)
    
- LVM (Logical Volume Manager)
    

---

## 5.7.2 Unattended Install

### Recommended Boot Parameters

```bash
preseed/url=https://static.offsec.com/offsec-courses/KLR/Binaries/preseed.cfg locale=en_US keymap=us hostname=kali domain=local.lan
```

Alternative:

```bash
preseed/url=https://static.offsec.com/offsec-courses/KLR/Binaries/preseed.cfg auto=true priority=critical
```

### Why Can't Preseed Handle Everything?

Because network-based preseed files are loaded later in the installation process. Early settings such as locale, keyboard layout, hostname, and domain must be provided through kernel boot parameters.

---

## 5.7.3 Standard ARM Install

### Start SSH

```bash
sudo systemctl start ssh
```

### Enable SSH at Boot

```bash
sudo systemctl enable ssh
```

### Transfer Image

```bash
scp kali-linux-2020.3-rpi3-nexmon.img.xz kali@TARGET_IP:/home/kali
```

### Find SD Card

```bash
sudo dmesg
```

### Extract Image

```bash
unxz kali-linux-2020.3-rpi3-nexmon.img.xz
```

### Write Image

```bash
sudo dd if=kali-linux-2020.3-rpi3-nexmon.img of=/dev/sdb bs=1M
```

---

## 5.7.4 Custom ARM Install

### Create Workspace

```bash
mkdir ~/kali/arm-stuff
cd ~/kali/arm-stuff
```

### Download Cross Compiler

```bash
git clone https://gitlab.com/kalilinux/packages/gcc-arm-linux-gnueabihf-4-7
```

### Configure PATH

```bash
export PATH=${PATH}:~/kali/arm-stuff/gcc-arm-linux-gnueabihf-4.7/bin
```

### Download Build Scripts

```bash
git clone https://gitlab.com/kalilinux/build-scripts/kali-arm
```

### Install Dependencies

```bash
sudo ./build-deps.sh
```

### Build Image

```bash
./rpi3-nexmon.sh 1.0
```

### Write Image

```bash
xzcat kali-1.0-rpi3-nexmon.img.xz | dd of=/dev/sdb bs=1M
```

---

## 5.7.5 ARM Chroot

### Install QEMU

```bash
sudo apt-get install qemu qemu-user qemu-user-static
```

### Create Mount Point

```bash
sudo mkdir /mnt/sd
```

### Mount Root Filesystem

```bash
sudo mount /dev/sdc2 /mnt/sd
```

### Mount Virtual Filesystems

```bash
sudo mount -t proc none /mnt/sd/proc
sudo mount -t sysfs none /mnt/sd/sys
sudo mount -o bind /dev /mnt/sd/dev
sudo mount -o bind /dev/pts /mnt/sd/dev/pts
```

### Copy QEMU Binary

```bash
sudo cp /usr/bin/qemu-arm-static /mnt/sd/usr/bin
```

### Enter Chroot

```bash
sudo LANG=C chroot /mnt/sd/
```

### Install Packages

```bash
apt-get update
apt-get install mlocate
apt-get install net-tools
apt-get install hostapd dnsmasq
```

### Exit Chroot

```bash
exit
```

### Unmount Filesystems

```bash
sudo umount /mnt/sd/dev/pts
sudo umount /mnt/sd/dev
sudo umount /mnt/sd/sys
sudo umount /mnt/sd/proc
sudo umount /mnt/sd
```