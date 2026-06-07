# 6.3.3 Configuring PostgreSQL Databases

> PostgreSQL is one of the most popular relational database systems in Linux. Many tools and applications don't store data in files—they store it in PostgreSQL databases.

---

# First: What Is a Database?

Without a database:

```text
Application
    ↓
Text Files
```

---

With a database:

```mermaid
flowchart TD

A["Application"]

--> B["PostgreSQL"]

--> C["Structured Data"]

```

---

Examples:

|Application|Stores In PostgreSQL|
|---|---|
|Metasploit|Hosts, exploits, sessions|
|King Phisher|Campaign data|
|Web Applications|Users, posts, settings|
|Monitoring Tools|Logs, metrics|
|ERP/CRM Systems|Business data|

---

# PostgreSQL Architecture

Think of PostgreSQL as a service running in the background.

```mermaid
flowchart TD

A["Application"]

--> B["PostgreSQL Server"]

--> C["Databases"]

--> D["Tables"]

--> E["Records"]

```

---

# Real World Analogy

Think of PostgreSQL like a building.

```mermaid
flowchart TD

A["PostgreSQL Server"]

--> B["Database 1"]

--> C["Database 2"]

--> D["Database 3"]

```

Inside each database:

```mermaid
flowchart TD

A["Database"]

--> B["Users Table"]

--> C["Products Table"]

--> D["Orders Table"]

```

---

# PostgreSQL Service

PostgreSQL runs as a Linux service.

Start it:

```bash
sudo systemctl start postgresql
```

---

Check status:

```bash
systemctl status postgresql
```

---

Visualization:

```mermaid
flowchart TD

A["systemctl start postgresql"]

--> B["PostgreSQL Service"]

--> C["Port 5432"]

--> D["Ready For Connections"]

```

---

# PostgreSQL Server Process

The main daemon is:

```text
postgres
```

Sometimes called:

```text
postmaster
```

Historically.

---

# PostgreSQL Terminology

This is where many beginners get confused.

---

## Linux User

Example:

```text
kali
postgres
root
```

Managed by Linux.

Stored in:

```text
/etc/passwd
```

---

## PostgreSQL User

Example:

```text
king_phisher
metasploit
webapp
```

Managed by PostgreSQL.

Stored inside PostgreSQL.

---

These are NOT the same.

---

Visualization

```mermaid
flowchart TD

A["Linux Users"]

--> B["root"]

--> C["kali"]

--> D["postgres"]

E["PostgreSQL Users"]

--> F["king_phisher"]

--> G["metasploit"]

--> H["webapp"]

```

---

# The Special postgres User

PostgreSQL automatically creates:

```text
postgres
```

---

This is equivalent to:

```text
root
```

for PostgreSQL.

---

Think:

|Linux|PostgreSQL|
|---|---|
|root|postgres|

---

Visualization

```mermaid
flowchart TD

A["postgres"]

--> B["Create Databases"]

--> C["Create Users"]

--> D["Grant Permissions"]

--> E["Delete Databases"]

```

---

# Why Use su - postgres?

Instead of:

```bash
sudo createuser
```

the common method is:

```bash
su - postgres
```

---

Why?

Because:

```text
postgres
```

already has:

```text
Full Database Permissions
```

---

Flow

```mermaid
flowchart TD

A["root"]

--> B["su - postgres"]

--> C["PostgreSQL Administrator"]

```

---

# PostgreSQL Connections

PostgreSQL accepts connections in two ways.

---

## Method 1: TCP

Network connection.

```mermaid
flowchart LR

A["Client"]

--> B["TCP Port 5432"]

--> C["PostgreSQL"]

```

---

Default port:

```text
5432
```

---

## Method 2: Unix Socket

Local filesystem socket.

```mermaid
flowchart LR

A["Local Program"]

--> B["Unix Socket"]

--> C["PostgreSQL"]

```

---

Socket file:

```text
/var/run/postgresql/.s.PGSQL.5432
```

---

# Why Two Connection Methods?

TCP:

```text
Network Access
```

---

Socket:

```text
Local Machine Access
```

---

Sockets are:

```text
Faster
More Secure
No Networking Required
```

---

# PostgreSQL Authentication

Authentication depends on connection method.

---

# Socket Authentication

Default:

```mermaid
flowchart TD

A["Unix User"]

--> B["Socket"]

--> C["PostgreSQL"]

--> D["Trust User"]

```

---

Example:

```bash
su - postgres
psql
```

No password needed.

---

Why?

Because Linux already authenticated you.

---

# TCP Authentication

Different.

```mermaid
flowchart TD

A["Client"]

--> B["TCP 5432"]

--> C["Username"]

--> D["Password"]

--> E["PostgreSQL"]

```

---

Requires:

```text
PostgreSQL Username
PostgreSQL Password
```

---

Not Linux credentials.

---

# Important Configuration Files

---

# postgresql.conf

Main PostgreSQL configuration.

Think:

```text
sshd_config
for PostgreSQL
```

---

Contains:

```text
Port
Listen Addresses
Memory
Logging
Performance
```

---

# listen_addresses

Example:

```conf
listen_addresses='localhost'
```

Meaning:

```text
Accept Local Connections Only
```

---

Visualization

```mermaid
flowchart LR

A["localhost"]

--> B["PostgreSQL"]

```

---

If changed:

```conf
listen_addresses='*'
```

Then:

```mermaid
flowchart LR

A["Any Host"]

--> B["PostgreSQL"]

```

---

# port

Default:

```conf
port = 5432
```

---

# unix_socket_directories

Controls:

```text
Where Socket Files Are Created
```

Example:

```text
/var/run/postgresql
```

---

# pg_hba.conf

Most important PostgreSQL security file.

---

Think:

```text
Firewall For Database Logins
```

---

Purpose:

```text
Who Can Connect?

From Where?

Using Which Authentication?
```

---

Visualization

```mermaid
flowchart TD

A["Connection Request"]

--> B["pg_hba.conf"]

--> C{"Allowed?"}

C -->|"Yes"| D["Authenticate"]

C -->|"No"| E["Reject"]

```

---

# Creating PostgreSQL Users

Command:

```bash
createuser
```

---

Example:

```bash
createuser -P king_phisher
```

---

What Happens?

```mermaid
flowchart TD

A["createuser"]

--> B["Create Role"]

--> C["Set Password"]

--> D["User Ready"]

```

---

# Why Does PostgreSQL Call Them Roles?

Modern PostgreSQL terminology:

```text
Role
```

instead of:

```text
User
```

---

For exams and troubleshooting:

```text
Role ≈ User
```

---

# The -P Option

```bash
createuser -P king_phisher
```

---

Meaning:

```text
Prompt For Password
```

---

Flow

```mermaid
flowchart TD

A["Create User"]

--> B["Enter Password"]

--> C["Store Hash"]

```

---

# Creating Databases

Command:

```bash
createdb
```

---

Example:

```bash
createdb \
-T template0 \
-E UTF-8 \
-O king_phisher \
king_phisher
```

---

Let's Decode It.

---

# Database Name

```text
king_phisher
```

Creates:

```text
Database:
king_phisher
```

---

# Owner

```bash
-O king_phisher
```

Means:

```text
Owner = king_phisher
```

---

Visualization

```mermaid
flowchart TD

A["Database"]

--> B["Owner"]

--> C["king_phisher"]

```

---

Owner gets:

```text
Create Tables
Delete Tables
Grant Permissions
Modify Data
```

---

# UTF-8

```bash
-E UTF-8
```

Means:

```text
Unicode Support
```

---

Supports:

```text
English
हिन्दी
日本語
中文
```

---

Without UTF-8:

Many languages break.

---

# Template Databases

This is unique to PostgreSQL.

---

When creating a database:

PostgreSQL actually copies another database.

---

Visualization

```mermaid
flowchart TD

A["Template Database"]

--> B["New Database"]

```

---

Default template:

```text
template1
```

---

Book uses:

```bash
-T template0
```

---

Why?

To create a clean database.

---

# Testing Connection

Command:

```bash
psql -h localhost -U king_phisher king_phisher
```

---

Breakdown:

```text
-h localhost
    Connect To Localhost

-U king_phisher
    Username

king_phisher
    Database Name
```

---

Visualization

```mermaid
flowchart TD

A["psql"]

--> B["localhost"]

--> C["PostgreSQL"]

--> D["Database"]

```

---

# What Is psql?

Equivalent of:

```text
Cisco CLI
```

for PostgreSQL.

---

It is the PostgreSQL shell.

Example:

```text
king_phisher=>
```

---

From here you run SQL commands.

---

# PostgreSQL Clusters

This is Debian/Kali specific.

---

Most people think:

```text
One PostgreSQL Server
```

---

Debian allows:

```mermaid
flowchart TD

A["Machine"]

--> B["Cluster 14"]

--> C["Cluster 15"]

--> D["Cluster 16"]

```

running simultaneously.

---

# What Is A Cluster?

In Debian:

```text
One PostgreSQL Instance
+
Its Databases
+
Its Configurations
```

---

Think:

```text
Independent PostgreSQL Server
```

---

# Why Multiple Clusters?

Example:

```text
PostgreSQL 14
PostgreSQL 15
```

running together.

---

Visualization

```mermaid
flowchart TD

A["Cluster 14"]

--> B["Port 5432"]

C["Cluster 15"]

--> D["Port 5433"]

```

---

# Cluster Configuration Location

```text
/etc/postgresql/version/cluster-name/
```

Example:

```text
/etc/postgresql/15/main/
```

---

# Viewing Clusters

Command:

```bash
pg_lsclusters
```

---

Output shows:

```text
Version
Cluster Name
Port
Status
```

---

Visualization

```mermaid
flowchart TD

A["pg_lsclusters"]

--> B["Version"]

--> C["Port"]

--> D["Status"]

```

---

# Managing Clusters

Common commands:

|Command|Purpose|
|---|---|
|pg_createcluster|Create Cluster|
|pg_dropcluster|Delete Cluster|
|pg_ctlcluster|Start/Stop Cluster|
|pg_upgradecluster|Upgrade Cluster|
|pg_renamecluster|Rename Cluster|
|pg_lsclusters|Show Clusters|

---

# PostgreSQL Upgrade Process

Example:

Current:

```text
Cluster 14
Port 5432
```

Install new version:

```text
Cluster 15
Port 5433
```

---

Migration:

```mermaid
flowchart TD

A["Old Cluster"]

--> B["pg_upgradecluster"]

--> C["New Cluster"]

```

---

After testing:

```bash
pg_dropcluster
```

removes old cluster.

---

# Complete PostgreSQL Architecture

```mermaid
flowchart TD

A["Applications"]

--> B["PostgreSQL"]

B --> C["TCP 5432"]

B --> D["Unix Socket"]

B --> E["Users / Roles"]

B --> F["Databases"]

F --> G["Tables"]

F --> H["Records"]

B --> I["pg_hba.conf"]

B --> J["postgresql.conf"]

B --> K["Cluster"]

```

---

# Commands To Remember

## Start PostgreSQL

```bash
systemctl start postgresql
```

---

## Switch To PostgreSQL Admin

```bash
su - postgres
```

---

## Create User

```bash
createuser -P username
```

---

## Create Database

```bash
createdb -O username dbname
```

---

## Connect To Database

```bash
psql -h localhost -U username dbname
```

---

## Show Clusters

```bash
pg_lsclusters
```

---

# Quick Memory Diagram

```mermaid
flowchart TD

A["PostgreSQL"]

--> B["postgres User"]

--> C["Roles"]

--> D["Databases"]

--> E["TCP 5432"]

--> F["Unix Socket"]

--> G["pg_hba.conf"]

--> H["postgresql.conf"]

--> I["Clusters"]

```

### Remember

```text
postgres
    = PostgreSQL root

5432
    = Default Port

createuser
    = Create Database User

createdb
    = Create Database

psql
    = PostgreSQL CLI

pg_hba.conf
    = Authentication Rules

postgresql.conf
    = Main Configuration

pg_lsclusters
    = Show PostgreSQL Instances
```