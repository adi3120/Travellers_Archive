# Section 9.3 — Advanced APT Configuration and Usage

Up to now you've learned:

```text
Repositories
APT
dpkg
Installation
Removal
Upgrades
Troubleshooting
Frontends
```

This section explains what separates:

```text
Normal Linux Users

from

Power Users
```

You'll learn:

```text
APT Configuration
Package Priorities
Mixing Distributions
Automatic Packages
Multi-Architecture
Package Authenticity
```

---

# Section 9.3.1 — Configuring APT

Most beginners think:

```text
APT Configuration
=
One Config File
```

Historically that was true.

Modern Debian systems use:

```text
Configuration Directories
```

instead.

---

# Traditional Configuration

```mermaid
flowchart TD

    A["Application"]

    B["Single Config File"]

    A --> B
```

Example:

```text
/etc/program.conf
```

---

# Modern Debian Configuration

Instead of:

```text
One Giant File
```

Debian uses:

```text
Many Small Files
```

---

```mermaid
flowchart TD

    A["APT"]

    B["apt.conf.d"]

    C["10proxy"]
    D["20cache"]
    E["99local"]

    A --> B

    B --> C
    B --> D
    B --> E
```

---

# The Important Directory

```text
/etc/apt/apt.conf.d/
```

APT reads every file inside.

---

# Processing Order

APT reads files:

```text
Alphabetically
```

Example:

```text
10proxy

20cache

50security

99local
```

---

```mermaid
flowchart LR

    A["10proxy"]

    B["20cache"]

    C["50security"]

    D["99local"]

    A --> B
    B --> C
    C --> D
```

Later files can override earlier settings.

---

# Why Debian Uses .d Directories

Without `.d`:

```text
Package A edits config

Package B edits config

Administrator edits config

Chaos
```

---

With `.d`:

```text
Each package drops
its own configuration file
```

No conflicts.

---

# Example APT Command Option

You already saw:

```bash
apt -o Dpkg::Options::="--force-overwrite" install zsh
```

---

Meaning:

```text
Temporarily pass
--force-overwrite
to dpkg
```

---

# Permanent Configuration

Instead of typing that every time:

Create:

```text
/etc/apt/apt.conf.d/99local
```

Contents:

```text
Dpkg::Options {
   "--force-overwrite";
}
```

---

# Configuration Hierarchy

```mermaid
flowchart TD

    A["Command Line"]

    B["apt.conf.d"]

    C["APT"]

    A --> C
    B --> C
```

---

# Proxy Configuration

Common enterprise scenario.

---

Without Proxy

```mermaid
flowchart LR

    A["APT"]

    B["Internet"]

    A --> B
```

---

With Proxy

```mermaid
flowchart LR

    A["APT"]

    B["Proxy"]

    C["Internet"]

    A --> B
    B --> C
```

---

# HTTP Proxy

```text
Acquire::http::proxy "http://proxy:3128";
```

---

# FTP Proxy

```text
Acquire::ftp::proxy "ftp://proxy";
```

---

# Finding More Options

Manual:

```bash
man apt.conf
```

---

# Mindmap

```mermaid
mindmap
  root((APT Configuration))

    apt.conf.d

      Multiple Files
      Alphabetical Order

    99local

      Custom Settings

    Dpkg Options

      force-overwrite

    Network

      HTTP Proxy
      FTP Proxy

    Documentation

      man apt.conf
```

---

# Section 9.3.2 — Managing Package Priorities (APT Pinning)

This is one of the most powerful APT features.

---

# The Problem

Suppose your repositories contain:

```text
Kali Rolling

Kali Dev

Debian Unstable

Debian Experimental
```

---

Question:

```text
Which package should APT choose?
```

---

APT uses:

```text
Priorities
```

---

# Package Selection Logic

```mermaid
flowchart TD

    A["Package Exists"]

    B["Kali Rolling"]

    C["Kali Dev"]

    D["Debian Unstable"]

    E["Priority Comparison"]

    F["Winner"]

    A --> B
    A --> C
    A --> D

    B --> E
    C --> E
    D --> E

    E --> F
```

---

# Default Priorities

|Situation|Priority|
|---|---|
|Installed Package|100|
|Non-installed Package|500|
|Target Release|990|

---

# Priority Rules

### Priority < 0

```text
Never Install
```

---

### 0 - 100

```text
Install only if nothing installed
```

---

### 100 - 500

```text
Prefer newer installed versions
```

---

### 500 - 990

```text
Normal repository packages
```

---

### 990 - 1000

```text
Preferred target release
```

---

### >1000

```text
Force installation

Even downgrade
```

---

# APT Preference File

Location:

```text
/etc/apt/preferences
```

or

```text
/etc/apt/preferences.d/
```

---

# Example: Prefer Kali

```text
Package: *
Pin: release o=Kali
Pin-Priority: 900
```

---

Block Debian:

```text
Package: *
Pin: release o=Debian
Pin-Priority: -10
```

---

# Pinning Concept

```mermaid
flowchart TD

    A["Repository"]

    B["Pin Rule"]

    C["Priority"]

    D["APT Decision"]

    A --> B
    B --> C
    C --> D
```

---

# Locking a Version

Example:

```text
Package: perl
Pin: version 5.22*
Pin-Priority: 1001
```

Meaning:

```text
Always keep Perl 5.22
```

---

# Useful Command

```bash
apt-cache policy
```

Shows repository priorities.

---

Specific package:

```bash
apt-cache policy nmap
```

---

```mermaid
flowchart TD

    A["apt-cache policy"]

    B["Repository Priorities"]

    C["Selected Version"]

    A --> B
    B --> C
```

---

# Section 9.3.3 — Working With Multiple Distributions

APT can mix:

```text
Kali Rolling

Kali Dev

Debian Unstable

Debian Experimental
```

---

# Multi-Distribution Layout

```mermaid
flowchart TD

    A["APT"]

    B["Kali Rolling"]

    C["Kali Dev"]

    D["Debian Unstable"]

    E["Debian Experimental"]

    A --> B
    A --> C
    A --> D
    A --> E
```

---

# Install From Another Distribution

Example:

```bash
apt install package/unstable
```

Meaning:

```text
Install package
from Debian Unstable
```

---

# Force Dependency Resolution

```bash
apt install package/unstable -t unstable
```

Meaning:

```text
Resolve dependencies
inside unstable
```

---

# Warning

Mixing repositories can create:

```text
Dependency Hell
```

---

```mermaid
flowchart TD

    A["Kali Package"]

    B["Debian Package"]

    C["Different Libraries"]

    D["Conflict"]

    A --> C
    B --> C
    C --> D
```

---

# Section 9.3.4 — Tracking Automatically Installed Packages

APT tracks:

```mermaid
mindmap
  root((Packages))

    Manual

      Installed By User

    Automatic

      Installed As Dependency
```

---

# Example

Install:

```bash
apt install nmap
```

---

APT also installs:

```text
libpcap

libssl

zlib
```

---

```mermaid
flowchart TD

    A["nmap"]

    B["libpcap"]

    C["libssl"]

    D["zlib"]

    A --> B
    A --> C
    A --> D
```

---

# Mark Automatic

```bash
apt-mark auto package
```

---

# Mark Manual

```bash
apt-mark manual package
```

---

# Remove Unused Dependencies

```bash
apt autoremove
```

---

```mermaid
flowchart TD

    A["Main Package Removed"]

    B["Dependencies Unused"]

    C["apt autoremove"]

    D["Removed"]

    A --> B
    B --> C
    C --> D
```

---

# Why Is Package Installed?

Very useful command:

```bash
aptitude why package
```

Example:

```bash
aptitude why python-debian
```

Output:

```text
Package A
depends on Package B
```

---

# Section 9.3.5 — Multi-Arch Support

Every package has an architecture.

Examples:

```text
amd64

i386

arm64

armhf
```

---

# Check Current Architecture

```bash
dpkg --print-architecture
```

Example:

```text
amd64
```

---

# Problem

Can an amd64 system run i386 software?

Answer:

```text
Yes
```

with Multi-Arch.

---

# Enable i386

```bash
sudo dpkg --add-architecture i386
```

---

Verify:

```bash
dpkg --print-foreign-architectures
```

Output:

```text
i386
```

---

# Multi-Arch Workflow

```mermaid
flowchart TD

    A["amd64 System"]

    B["Enable i386"]

    C["apt update"]

    D["Install i386 Packages"]

    A --> B
    B --> C
    C --> D
```

---

# Install Specific Architecture

```bash
apt install wine32:i386
```

---

Syntax:

```text
package:architecture
```

---

# Why Multi-Arch Exists

Most common use:

```text
Run 32-bit software
on 64-bit systems
```

Examples:

```text
Wine

Old Games

Legacy Software
```

---

# Section 9.3.6 — Validating Package Authenticity

This is one of the most important security features in Linux.

---

# The Threat

Suppose:

```text
Attacker Compromises Mirror
```

and replaces:

```text
nmap.deb
```

with:

```text
malicious nmap.deb
```

---

How does APT detect it?

---

# Trust Chain

```mermaid
flowchart TD

    A["Release File"]

    B["Package List"]

    C["Package Hash"]

    D["Deb Package"]

    E["APT Verification"]

    A --> B
    B --> C
    C --> D
    D --> E
```

---

# Verification Layers

```text
Release.gpg Signature

↓

Packages Hashes

↓

Deb Package Hashes
```

---

# Trusted Keys

Stored in:

```text
/etc/apt/trusted.gpg.d/
```

---

# View Trusted Keys

```bash
apt-key fingerprint
```

_(Modern systems warn that apt-key is deprecated.)_

---

# Package Authenticity Workflow

```mermaid
flowchart TD

    A["Download Release File"]

    B["Verify Signature"]

    C["Verify Package Lists"]

    D["Verify Package Hash"]

    E["Install"]

    A --> B
    B --> C
    C --> D
    D --> E
```

---

# Adding Third-Party Repositories

APT must trust the repository's key.

Historically:

```bash
apt-key add key.asc
```

---

Modern approach:

```text
Store GPG keys
inside trusted.gpg.d
```

---

# Why Signatures Matter

Without signatures:

```text
Anyone could modify packages
```

---

With signatures:

```text
APT knows:

Who signed package metadata

Whether files changed

Whether repository is trusted
```

---

# Mindmap Summary

```mermaid
mindmap
  root((Advanced APT))

    Configuration

      apt.conf.d
      Proxy
      99local

    Package Priorities

      Pinning
      Preferences
      apt-cache policy

    Multiple Distributions

      Kali Rolling
      Kali Dev
      Debian Unstable
      Debian Experimental

    Automatic Packages

      apt-mark auto
      apt-mark manual
      apt autoremove
      aptitude why

    MultiArch

      amd64
      i386
      add-architecture

    Authenticity

      GPG Keys
      Release Files
      Package Hashes
      Signatures
```

---

# Commands Worth Memorizing

```bash
man apt.conf
apt-cache policy
apt-cache policy package
apt-mark auto package
apt-mark manual package
apt autoremove
aptitude why package
dpkg --print-architecture
dpkg --add-architecture i386
dpkg --print-foreign-architectures
apt-key fingerprint
```

This chapter is where APT goes from **"I can install packages"** to **"I can completely control how packages are selected, trusted, prioritized, mixed across distributions, and installed across architectures."**