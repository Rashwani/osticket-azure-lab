[README.md](https://github.com/user-attachments/files/25827262/README.md)
# 🎫 osTicket Help Desk Deployment on Microsoft Azure

![osTicket](https://img.shields.io/badge/osTicket-v1.18.1-blue) ![Azure](https://img.shields.io/badge/Microsoft-Azure-0089D6?logo=microsoft-azure) ![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04_LTS-E95420?logo=ubuntu) ![LAMP](https://img.shields.io/badge/Stack-LAMP-green)

---

## 📋 Project Overview

This project documents the end-to-end deployment of **osTicket v1.18.1** — an open-source IT help desk ticketing system — on a **Microsoft Azure Ubuntu VM** using a full LAMP stack (Linux, Apache, MySQL, PHP).

This lab mirrors how enterprise IT support teams operate. A centralized ticketing system with SLA enforcement, tiered agent roles, and department-based routing reduces resolution time, creates accountability, and produces an auditable trail of every support interaction. Deploying this on Azure demonstrates cloud infrastructure skills and an understanding of network security groups, public IPs, and cloud VM management that translate directly to real workplace environments.

---

## ⚙️ Environment Summary

| Component | Detail |
|-----------|--------|
| **Platform** | Microsoft Azure (Free Tier eligible) |
| **OS** | Ubuntu Server 22.04 LTS |
| **Web Server** | Apache2 |
| **Database** | MySQL Server |
| **App** | osTicket v1.18.1 |
| **Estimated Build Time** | 2–3 hours |
| **Skill Level** | Beginner / Intermediate |

---

## 🏗️ Architecture Diagram

```
Client Browser
    ↓ HTTP (Port 80)
Azure NSG (Network Security Group)
    ↓ Allowed: SSH (22), HTTP (80)
Ubuntu Server 22.04 LTS (Azure VM)
    ↓
Apache2 Web Server
    ↓
osTicket (PHP Application)
    ↓
MySQL Database
```

---

## 📸 Phase 1 — Azure VM Provisioning

### 1.1 VM Configuration & Architecture Overview

Provisioned a `Standard_B1s` VM (1 vCPU, 1 GB RAM) running Ubuntu 22.04 LTS — free tier eligible.


---

### 1.2 Network Security Group (NSG) Rules

Configured inbound rules to allow SSH (port 22) and HTTP (port 80).


---

## 🔧 Phase 2 — LAMP Stack Installation

### 2.1 NSG Confirmed + LAMP Install Started

Both SSH and HTTP inbound rules confirmed active. Began LAMP stack installation.


---

### 2.2 Apache Default Page Verified + MySQL & PHP Install

Apache verified running by loading the VM's public IP in a browser. MySQL and PHP extensions installed.


---

## 🗄️ Phase 3 — MySQL Database Setup

### 3.1 Database and User Created

```sql
CREATE DATABASE osticket;
CREATE USER 'osticketuser'@'localhost' IDENTIFIED BY 'StrongPassword123!';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticketuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```


---

## 📦 Phase 4 — osTicket Installation

### 4.1 Web Installer

Navigated to `http://YOUR_PUBLIC_IP/osticket/setup/` and completed the installation wizard with system settings, admin account, and database credentials.


---

### 4.2 Installation Success

Received the green Congratulations screen. Immediately removed the `/setup` directory and locked down `ost-config.php`.

```bash
sudo rm -rf /var/www/html/osticket/setup
sudo chmod 0644 /var/www/html/osticket/include/ost-config.php
```


---

## 🏢 Phase 5 — Help Desk Configuration

### 5.1 Departments Created

Navigated to **Admin Panel → Agents → Departments** and created four routing departments.

| Department | Purpose |
|------------|---------|
| IT Support (Tier 1) | Front-line support for general issues |
| Networking | Network connectivity and infrastructure issues |
| Security | Security incidents, access control, account issues |
| Hardware | Physical device issues, replacements, peripherals |


---

### 5.2 SLA Plans Configured

Navigated to **Admin Panel → Manage → SLA Plans** and created three SLA tiers.

| SLA Plan | Grace Period | Schedule |
|----------|-------------|----------|
| High Priority SLA | 4 hours | 24/7 |
| Normal Priority SLA | 24 hours | 24/7 |
| Low Priority SLA | 72 hours | 24/7 |


---

### 5.3 Help Topics & Agent Accounts

Created 5 Help Topics (Password Reset, Network/Wi-Fi Issues, VPN/Remote Access, Hardware Request, Account Lockout) and provisioned two agent accounts: **Jane Smith** (Tier 1) and **John Doe** (Senior/Full Access).



---

## 🎟️ Phase 6 — Ticket Workflow Simulation

### Scenario 1: VPN Not Connecting

**Priority:** High | **SLA:** 4 hours | **Department:** Networking

A user submitted a ticket via the client portal reporting VPN error 800 after a Windows update.


Jane Smith (Tier 1) opened the ticket, set High priority, added an internal investigation note, then escalated to John Doe. John resolved by rolling back Windows update `KB5034441` via remote session.


---

### Scenario 2: Account Lockout

**Priority:** High | **Department:** Security

User locked out after repeated failed login attempts before a meeting. Agent verified identity via secondary email, unlocked the AD account, and advised on password manager usage.



---

### Scenario 3: Email Not Syncing

**Priority:** Normal | **Department:** IT Support (Tier 1)

Outlook showed "Disconnected" status. Agent re-entered Exchange credentials, checked server status, and cleared the Outlook cache (`.ost` file).


---

## 🧠 Challenges & Lessons Learned

- **Apache/PHP not loading osTicket** — Resolved by enabling `mod_rewrite` (`sudo a2enmod rewrite`) and restarting Apache. Learned that Apache modules must be explicitly enabled even after PHP install.
- **MySQL permissions error during install** — Forgot to run `FLUSH PRIVILEGES` after the `GRANT` statement. This is a critical step — MySQL caches privilege tables and won't apply changes without it.
- **Setup directory left open** — Caught during self-review. Added a post-installation checklist to always remove `/setup` immediately. Leaving it open is a real security vulnerability.
- **NSG rule not applying** — HTTP traffic was blocked because the rule was applied to the NIC instead of the NSG. Learned the architectural difference between Azure NIC-level and NSG-level rules.

---

## 🌍 Real-World Relevance

This lab mirrors how enterprise IT support teams operate. A centralized ticketing system with SLA enforcement, tiered agent roles, and department-based routing reduces resolution time, creates accountability, and produces an auditable trail of every support interaction. Deploying this on Azure — rather than locally — demonstrates cloud infrastructure skills and an understanding of network security groups, public IPs, and cloud VM management that translate directly to real workplace environments.


