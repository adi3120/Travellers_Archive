# Section 10.1.5 — Patches and Quilt (The Debian Way of Modifying Source Code)

This is the section where most people get confused because they think:

```text
Need to modify source?

Open file

Edit file

Build package
```

Why do we need:

```text
patches
quilt
series
```

at all?

---

# First Understand The Problem

Suppose Debian packages:

```text
nmap 7.95
```

You find a bug.

You directly edit:

```text
src/output.cc
```

and fix it.

---

```mermaid
flowchart TD

    A["Original Source"]

    B["Edit File"]

    C["Fixed Source"]

    A --> B
    B --> C
```

Looks fine.

---

# Six Months Later...

Upstream releases:

```text
nmap 7.96
```

You download it.

Your modifications disappear.

---

```mermaid
flowchart TD

    A["nmap 7.95"]

    B["Manual Fix"]

    C["nmap 7.96"]

    D["Fix Lost"]

    A --> B
    B --> C
    C --> D
```

---

# Debian's Solution

Instead of changing source directly:

Store changes separately.

---

Think:

```text
Original Source

+

Patch File

=

Modified Source
```

---

```mermaid
flowchart TD

    A["Original Source"]

    B["Patch"]

    C["Modified Source"]

    A --> B
    B --> C
```

---

# What Is A Patch?

A patch is simply:

```text
Difference Between

Old File

and

New File
```

---

Example

Original:

```c
printf("Hello");
```

Modified:

```c
printf("Hello World");
```

Patch stores only:

```text
Remove:
Hello

Add:
Hello World
```

---

# Why Is This Awesome?

Instead of storing:

```text
Entire Source Tree
```

Debian stores:

```text
Only Changes
```

---

```mermaid
flowchart TD

    A["10000 Source Files"]

    B["1 Bug Fix"]

    C["Patch"]

    A --> B
    B --> C
```

---

# Where Are Patches Stored?

Inside:

```text
debian/patches/
```

---

Example:

```text
debian/

└── patches/

    fix-crash.patch

    security-fix.patch

    improve-logging.patch

    series
```

---

# What Is series?

This is extremely important.

It tells Debian:

```text
Apply Patch 1

Then Patch 2

Then Patch 3
```

---

Example:

```text
fix-crash.patch
security-fix.patch
improve-logging.patch
```

---

```mermaid
flowchart TD

    A["Original Source"]

    B["Patch 1"]

    C["Patch 2"]

    D["Patch 3"]

    E["Final Source"]

    A --> B
    B --> C
    C --> D
    D --> E
```

---

# Why Multiple Patches?

Imagine:

```text
Patch 1
Fixes Memory Leak

Patch 2
Adds Logging

Patch 3
Fixes Security Issue
```

Keeping them separate makes maintenance easier.

---

# Enter Quilt

Quilt is Debian's patch management tool.

Think:

```text
Git

for

Package Patches
```

(Not exactly Git, but similar idea.)

---

# What Does Quilt Do?

Without Quilt:

```text
Create Patch

Apply Patch

Remove Patch

Manage Patch Order

Manually
```

Painful.

---

With Quilt:

```text
Automated
```

---

```mermaid
flowchart TD

    A["Quilt"]

    B["Create Patch"]

    C["Apply Patch"]

    D["Remove Patch"]

    E["Manage Stack"]

    A --> B
    A --> C
    A --> D
    A --> E
```

---

# Viewing Existing Patches

Inside source directory:

```bash
quilt series
```

Output:

```text
fix-crash.patch
security.patch
logging.patch
```

---

Meaning:

```text
These patches exist

in this order
```

---

# See Applied Patches

```bash
quilt applied
```

---

Output:

```text
fix-crash.patch
security.patch
```

---

Meaning:

```text
Already Applied
```

---

# See Unapplied Patches

```bash
quilt unapplied
```

---

Output:

```text
logging.patch
```

---

Meaning:

```text
Still Waiting
```

---

# Applying All Patches

```bash
quilt push -a
```

---

Think:

```text
Apply Everything
```

---

```mermaid
flowchart TD

    A["Patch 1"]

    B["Patch 2"]

    C["Patch 3"]

    D["Source Updated"]

    A --> B
    B --> C
    C --> D
```

---

# Removing All Patches

```bash
quilt pop -a
```

---

Think:

```text
Return To Original Source
```

---

```mermaid
flowchart TD

    A["Modified Source"]

    B["Remove Patches"]

    C["Original Source"]

    A --> B
    B --> C
```

---

# Creating Your Own Patch

This is the workflow you'll actually use.

---

## Step 1

Create patch:

```bash
quilt new my-fix.patch
```

---

Now Quilt knows:

```text
A new patch is being created
```

---

## Step 2

Tell Quilt which file you will edit.

Example:

```bash
quilt add src/main.c
```

---

Meaning:

```text
Track changes
to this file
```

---

## Step 3

Edit file.

Example:

```c
printf("Hello");
```

becomes:

```c
printf("Hello World");
```

---

## Step 4

Save changes.

Then:

```bash
quilt refresh
```

---

Quilt generates:

```text
my-fix.patch
```

automatically.

---

```mermaid
flowchart TD

    A["quilt new"]

    B["quilt add"]

    C["Edit File"]

    D["quilt refresh"]

    E["Patch Created"]

    A --> B
    B --> C
    C --> D
    D --> E
```

---

# Viewing Patch Contents

```bash
quilt diff
```

---

Output:

```diff
- printf("Hello");
+ printf("Hello World");
```

---

This is literally what a patch is.

---

# Why Debian Uses Quilt

Imagine Debian maintains:

```text
500 Changes
```

to Firefox.

---

Instead of changing source directly:

```text
500 Separate Patches
```

can be:

```text
Applied

Removed

Reordered

Reviewed
```

independently.

---

# Real Debian Workflow

```mermaid
flowchart TD

    A["Upstream Source"]

    B["Debian Patches"]

    C["Patched Source"]

    D["Build Package"]

    A --> B
    B --> C
    C --> D
```

---

# Upgrading To New Version

This is where Quilt shines.

---

Suppose:

```text
nmap 7.95
```

contains:

```text
10 Debian Patches
```

---

New release:

```text
nmap 7.96
```

arrives.

---

Debian simply:

```text
Apply Existing Patches
```

to new source.

---

```mermaid
flowchart TD

    A["nmap 7.95"]

    B["10 Patches"]

    C["nmap 7.96"]

    D["Reapply Patches"]

    A --> B
    B --> C
    C --> D
```

---

# Patch Failure

Sometimes:

```text
Source Changed Too Much
```

---

Patch no longer matches.

---

Example:

```text
Old Function Removed
```

Patch tries to modify it.

---

Result:

```text
Patch Failed
```

---

This is called:

```text
Patch Conflict
```

---

Exactly like:

```text
Git Merge Conflict
```

---

# Typical Developer Workflow

Download source:

```bash
apt source package
```

---

Apply existing patches:

```bash
quilt push -a
```

---

Create patch:

```bash
quilt new fix.patch
```

---

Track file:

```bash
quilt add src/file.c
```

---

Edit file.

---

Generate patch:

```bash
quilt refresh
```

---

Build package:

```bash
dpkg-buildpackage -rfakeroot
```

---

```mermaid
flowchart TD

    A["apt source"]

    B["quilt push -a"]

    C["quilt new"]

    D["quilt add"]

    E["Edit"]

    F["quilt refresh"]

    G["Build"]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
```

---

# Most Important Commands

Show patches:

```bash
quilt series
```

---

Show applied patches:

```bash
quilt applied
```

---

Apply all:

```bash
quilt push -a
```

---

Remove all:

```bash
quilt pop -a
```

---

Create patch:

```bash
quilt new fix.patch
```

---

Track file:

```bash
quilt add file.c
```

---

Save patch:

```bash
quilt refresh
```

---

Show patch differences:

```bash
quilt diff
```

---

# Mental Model

```text
Original Source
        +
Patch Files
        ↓

Patched Source
        ↓

dpkg-buildpackage
        ↓

.deb Package
```

---

# Mindmap Summary

```mermaid
mindmap
  root((Quilt))

    Patches

      Bug Fixes
      Security Fixes
      Custom Changes

    Storage

      debian/patches

    Series

      Patch Order

    Commands

      quilt new
      quilt add
      quilt refresh
      quilt push
      quilt pop

    Purpose

      Maintain Changes
      Upgrade Easily
      Separate Upstream From Debian
```

---

At this point you now understand the **complete Debian maintainer workflow**:

```text
Get Source
↓
Install Build Dependencies
↓
Understand debian/
↓
Create/Manage Patches
↓
Build Package
↓
Generate .deb
↓
Install/Test
```

This is the same workflow used by Kali and Debian maintainers when they customize upstream software into Debian packages.