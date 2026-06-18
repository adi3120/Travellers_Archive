This chapter becomes much more valuable if you stop thinking of it as:

> "create a user, add sudo, change shell"

and instead treat it like:

> **"You are the Linux administrator of a multi-user Kali jump host used by a red team."**

That lets you touch:

- Users
    
- Groups
    
- Passwords
    
- Expiration
    
- Locking accounts
    
- Shared directories
    
- GECOS fields
    
- NSS / getent
    
- `/etc/passwd`
    
- `/etc/shadow`
    
- `/etc/group`
    
- `/etc/gshadow`
    
- sudo delegation
    
- setgid
    
- group passwords
    
- shell restrictions
    
- auditing
    
- permissions
    
- account lifecycle
    

---

# 4-Hour Lab: Kali Red Team Jump Host Administration

## Scenario

You have been hired to administer a Kali Linux jump server used by a red team.

The company has:

|Team|Purpose|
|---|---|
|Operators|Conduct assessments|
|Developers|Write tooling|
|Analysts|Review findings|
|Contractors|Temporary access|
|Interns|Limited access|

You must build and manage the environment from scratch.

---

# Phase 1 - User Enumeration and Database Analysis

Duration: 30 minutes

---

## Task 1

Document:

```bash
/etc/passwd
/etc/shadow
/etc/group
/etc/gshadow
```
![[Pasted image 20260617193009.png]]
```bash
Field 1 = Username

Field 2 = Password Placeholder

Field 3 = UID

Field 4 = GID

Field 5 = Description

Field 6 = Home Directory

Field 7 = Login Shell
```


Questions:

- What does each file do?
    
- Which users can login?
> ![[Pasted image 20260617193946.png]]
    
- Which users are service accounts?
- ![[Pasted image 20260617194754.png]]
    ![[Pasted image 20260617195220.png]]
- Which accounts have `/usr/sbin/nologin`?
![[Pasted image 20260617195251.png]]

---

## Task 2

Use:

```bash
getent passwd
getent group
```

Count:

- Total users - 59
    ![[Pasted image 20260617200155.png]]
- Total groups 91
    ![[Pasted image 20260617200303.png]]
- Human users
    ![[Pasted image 20260617200451.png]]
- System users
    ![[Pasted image 20260617200530.png]]

---

## Task 3

For every user:

```bash
getent passwd username
id username
```

Create a table.

| User | UID | GID | Shell | Home |
| ---- | --- | --- | ----- | ---- |
![[Pasted image 20260617201711.png]]
---

## Task 4

Identify:

```bash
UID 0 users
```

Determine whether any account besides root has UID 0.
![[Pasted image 20260617201810.png]]
---

# Phase 2 - User Provisioning

Duration: 45 minutes

---

Create users:

```text
alice
bob
charlie
dave
eve
frank
```

---

## Requirements

### Alice

- Bash shell
    
- Sudo access
    

---

### Bob

- Bash shell
    
- No sudo
    

---

### Charlie

- Zsh shell
    

---

### Dave

- Expires in 14 days
    

---

### Eve

- Password must change at next login
    

---

### Frank

- Disabled account
    

---

## Verification

Show:

```bash
getent passwd
id
chage -l
```

for each user.

---

# Phase 3 - Group Management

Duration: 45 minutes

---

Create groups:

```text
redteam
developers
analysts
contractors
```

---

Assign users:

|User|Groups|
|---|---|
|alice|redteam|
|bob|redteam|
|charlie|developers|
|dave|analysts|
|eve|contractors|
|frank|contractors|

---

Verify:

```bash
id username
```

---

## Group Passwords

Set a password on:

```text
developers
```

using:

```bash
gpasswd developers
```

---

Test:

```bash
newgrp developers
```

from a non-member account.

---

Document behavior.

---

# Phase 4 - Shared Project Directories

Duration: 45 minutes

---

Create:

```text
/projects/redteam
/projects/developers
/projects/analysts
```

---

Requirements

### Redteam

Only:

```text
alice
bob
```

should access.

---

### Developers

Only:

```text
charlie
```

and root.

---

### Analysts

Only:

```text
dave
```

and root.

---

Use:

```bash
chmod
chown
chgrp
```

appropriately.

---

## Challenge

Configure:

```bash
chmod g+s
```

on project directories.

---

Verify:

Files created inside automatically inherit group ownership.

---

Explain:

```text
setgid
```

behavior.

---

# Phase 5 - Account Security Operations

Duration: 30 minutes

---

A contractor leaves the company.

Lock:

```text
eve
```

---

Verify:

```bash
passwd -l eve
```

---

Attempt login.

Document result.

---

Re-enable account.

```bash
passwd -u eve
```

---

Verify access restored.

---

# Phase 6 - Password Auditing

Duration: 20 minutes

---

For every user:

Show:

```bash
chage -l username
```

---

Configure:

### Alice

Password expires every:

```text
30 days
```

---

### Bob

Password expires every:

```text
60 days
```

---

### Charlie

Password expires every:

```text
90 days
```

---

Verify.

---

# Phase 7 - Shell Management

Duration: 20 minutes

---

View:

```bash
cat /etc/shells
```

---

Change:

```text
bob -> /bin/sh
charlie -> /bin/bash
dave -> /usr/sbin/nologin
```

---

Verify:

```bash
getent passwd
```

---

Attempt login as Dave.

Document behavior.

---

# Phase 8 - Sudo Delegation

Duration: 20 minutes

---

Create group:

```text
pentesters
```

---

Add:

```text
alice
bob
```

---

Modify sudoers safely:

```bash
visudo
```

---

Allow:

```text
pentesters
```

to run:

```bash
/usr/bin/nmap
```

without password.

---

Verify.

---

# Phase 9 - Advanced Investigation

Duration: 30 minutes

---

A suspicious account appears:

```text
backupsvc
```

---

Create it.

---

Hide it among system users:

```bash
useradd -r backupsvc
```

---

Investigate:

```bash
getent passwd
```

```bash
id backupsvc
```

```bash
grep backupsvc /etc/passwd
```

---

Determine:

- UID range
    
- Home directory
    
- Shell
    
- Login capability
    

---

# Phase 10 - Final Incident Response Scenario

Duration: 35 minutes

---

Assume:

```text
bob
```

has been compromised.

Perform:

### Evidence Collection

```bash
id bob
```

```bash
groups bob
```

```bash
chage -l bob
```

```bash
last bob
```

---

### Containment

Lock account.

```bash
passwd -l bob
```

---

### Preserve Access

Archive:

```bash
/home/bob
```

into:

```bash
/root/incident-bob.tar.gz
```

---

### Recovery

Reset password.

Unlock account.

Force password change.

---

Verify:

```bash
passwd -u bob
passwd -e bob
```

---

# Bonus Expert Challenges

1. Create a user with UID 1500 manually.
    
2. Create a group with GID 3000.
    
3. Create a user without a home directory.
    
4. Create a user with `/bin/false`.
    
5. Configure a shared directory using ACLs (`setfacl`).
    
6. Compare ACLs versus traditional Unix permissions.
    
7. Create a group-admin account capable of managing users without full root access.
    
8. Use `vipw` and `vigr` to modify databases safely.
    
9. Break `/etc/passwd` intentionally and recover it.
    
10. Create a complete user lifecycle:
    

```text
Create
Modify
Expire
Lock
Unlock
Delete
Archive
```

---

By the end of this lab you'll have used almost every concept in sections **6.2.x and 6.6.x**, and you'll understand Linux user/group administration at the level expected of a Linux administrator rather than just someone following commands.