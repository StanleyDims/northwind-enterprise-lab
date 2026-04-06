# Lab Setup Guide — Northwind Enterprise Lab

## Overview

This document provides a step-by-step guide to building the Northwind Enterprise Lab from scratch.

The setup simulates a real-world enterprise environment using:

- Proxmox VE (virtualization platform)  
- pfSense (firewall and router)  
- Linux and Windows virtual machines  
- Segmented networks  
- Active Directory Domain Services  
- DNS and DHCP integration  
- Group Policy  
- File services  

This guide reflects the current lab state after Phase 1 and Phase 2.

---

## Prerequisites

### Hardware

- Physical server (e.g., Fujitsu / Dell / HP)  
- Minimum:
  - 32 GB RAM  
  - 4+ CPU cores  
  - 500 GB+ storage  

---

### Software

- Proxmox VE ISO  
- pfSense ISO  
- Ubuntu Server ISO  
- Windows Server ISO  
- Windows Client ISO  
- VirtIO drivers ISO  

---

## Step 1 — Install Proxmox VE

1. Download Proxmox VE ISO  
2. Create bootable USB  
3. Boot server from USB  
4. Follow installation wizard:
   - Select disk  
   - Set root password  
   - Configure network  

5. After installation, access the web UI:

```text
https://<proxmox-ip>:8006
```

---

## Step 2 — Configure Proxmox Storage

Default storage:

| Storage   | Purpose               |
|----------|----------------------|
| local    | ISO images, backups  |
| local-lvm| VM disks             |

Upload required ISOs:

```
Node → local → ISO Images → Upload
```

---

## Step 3 — Create Network Bridges

Go to:

```
Node → Network
```

Create:

| Bridge | Purpose          |
|--------|------------------|
| vmbr0  | WAN (physical NIC) |
| vmbr1  | Server network     |
| vmbr2  | Client network     |
| vmbr3  | DMZ network        |

Apply configuration and restart network if required.

---

## Step 4 — Deploy pfSense

### Create VM

Recommended configuration:

- CPU: 2 cores  
- RAM: 2 GB  
- Disk: 20 GB  

Network:
- Adapter 1 → vmbr0 (WAN)  
- Adapter 2 → vmbr1 (Server)  
- Adapter 3 → vmbr2 (Client)  
- Adapter 4 → vmbr3 (DMZ)  

### Install pfSense

- Boot from ISO  
- Follow installation  
- Assign interfaces  

### Configure Interfaces

| Interface | IP           |
|----------|--------------|
| Server   | 10.10.20.1   |
| Client   | 10.10.30.1   |
| DMZ      | 10.10.40.1   |

### Enable Services

- DHCP on Client network  
- DNS Resolver  
- Enable forwarding mode if pfSense is behind NAT  

### Configure Firewall

- Allow Server → any  
- Allow Client → any  
- Restrict DMZ → internal networks  
- Block WAN inbound by default  

---

## Step 5 — Create Linux Template VM (NW-LX01)

### VM Configuration

- CPU: 2 cores  
- RAM: 4 GB  
- Disk: 30–64 GB  
- Network: vmbr1  

### Install Ubuntu Server

- Boot from ISO  
- Configure:
  - Username  
  - Hostname  
- Install OpenSSH  

### Verify Network

```bash
ip a
ping 8.8.8.8
ping google.com
```

---

## Step 6 — Install NGINX

```bash
sudo apt update
sudo apt install nginx -y
```

Verify:

```bash
systemctl status nginx
```

---

## Step 7 — Convert Linux VM to Template and Clone

### Prepare Linux VM
- Convert to template  
- Clone additional Linux VM(s) as needed  

Examples:
- NW-LX02  
- NW-LX03 (restored / recovery test)  

### Post-Clone Checks

- Verify IP address  
- Verify network interface  
- Remove MAC-specific binding if necessary  
- Confirm hostname  

---

## Step 8 — Create Windows Client VM (NW-WKS01)

### VM Configuration

- CPU: 2 cores  
- RAM: 4 GB  
- Disk: 30–64 GB  
- Network: vmbr2  

### Install Windows

- Attach Windows ISO  
- Install OS  
- Install VirtIO drivers if required  

### Initial Validation

From Windows:

- Verify IP assignment  
- Test internet access  
- Test access to Linux-hosted NGINX  

---

## Step 9 — Create Domain Controller VM (NW-DC01)

### VM Configuration

- CPU: 2 cores  
- RAM: 4–6 GB  
- Disk: 40–80 GB  
- Network: vmbr1  

### Install Windows Server

- Install OS  
- Rename server to `NW-DC01`  

### Set Static IP Configuration

```text
IP: 10.10.20.102
Subnet Mask: 255.255.255.0
Gateway: 10.10.20.1
Preferred DNS: 10.10.20.102
```

### Install Roles

- Active Directory Domain Services  
- DNS Server  

Promote server to Domain Controller:

```text
New forest: northwind.local
```

### Validate

```powershell
hostname
ipconfig
nslookup northwind.local
```

---

## Step 10 — Create File Server VM (NW-FS01)

### VM Configuration

- CPU: 2 cores  
- RAM: 4 GB  
- Disk: 40–80 GB  
- Network: vmbr1  

### Install Windows Server

- Rename server to `NW-FS01`  
- Set static IP on server network  
- Set preferred DNS to NW-DC01  

### Configure File Shares

Example local folder structure:

```text
C:\Shares\Finance
C:\Shares\HR
```

Publish SMB shares:

```text
\\NW-FS01\Finance
\\NW-FS01\HR
```

Apply:

- Share permissions  
- NTFS permissions  
- Group-based access control  

---

## Step 11 — Integrate DHCP and DNS Properly

### Important Design

Do not manually configure DNS on each client long-term.

Instead:

- Keep DHCP on pfSense  
- Configure pfSense DHCP (Client network) to hand out the Domain Controller as DNS  

### Correct Client Design

```text
Client IP: DHCP from pfSense
Gateway: 10.10.30.1
DNS: 10.10.20.102
```

### Validation on Client

```powershell
ipconfig /all
nslookup northwind.local
```

Expected:

- DNS server = Domain Controller  
- Domain resolves successfully  

---

## Step 12 — Build Active Directory Structure

### Create OU Structure

- Northwind Users  
  - IT  
  - HR  
  - Finance  
  - Engineering  
  - Support  

- Northwind Computers  
  - Workstations  
  - Servers  

- Northwind Groups  

### Create Sample Users

- john.it  
- sarah.hr  
- mike.finance  
- emma.support  
- david.eng  

### Create Security Groups

- IT_Admins  
- HR_Team  
- Finance_Team  
- Engineering_Team  
- Support_Team  

Recommended model:

```text
Users → Groups → Permissions
```

---

## Step 13 — Join Client to Domain

On `NW-WKS01`:

- Ensure DNS points to the Domain Controller  
- Open System Properties  
- Join domain:

```text
northwind.local
```

- Reboot  
- Sign in with a domain user  

Example:

```text
northwind\john.it
```

---

## Step 14 — Configure Group Policy

Create and apply GPOs such as:

- Password policy  
- Account lockout policy  
- Control Panel restriction (HR OU)  
- RDP restriction for IT_Admins  
- Application restriction for standard users  
- Drive mapping for file shares  

### Validate on Client

```powershell
gpupdate /force
gpresult /r
```

---

## Step 15 — Configure Drive Mapping

Map drives via GPO using UNC paths:

```text
\\NW-FS01\Finance
\\NW-FS01\HR
```

Do not use:

```text
C:\Shares\Finance
```

Use group-based targeting where appropriate.

---

## Step 16 — Configure Backup

```
Datacenter → Backup → Add
```

Configure:

- Target VMs  
- Schedule  
- Mode: snapshot  
- Compression: zstd  

### Test Backup

- Run backup manually  
- Verify backup file exists  

### Test Restore

- Restore to new VM ID  
- Validate service availability after restore  

---

## Step 17 — Snapshot Operations

### Create Snapshot

```bash
qm snapshot <vmid> <name>
```

### List Snapshots

```bash
qm listsnapshot <vmid>
```

### Rollback Snapshot

```bash
qm rollback <vmid> <name>
```

Use snapshots before:

- domain changes  
- GPO changes  
- file services changes  
- major network changes  

---

## Step 18 — Storage Lab

### Simulate Disk Usage

```bash
fallocate -l 5G bigfile
```

### Resize Disk

- Increase disk size in Proxmox  
- Extend filesystem or LVM inside guest OS  

---

## Step 19 — Resource Management

### Install Stress Tool

```bash
sudo apt install stress -y
```

### Simulate Load

```bash
stress --cpu 2
stress --vm 1 --vm-bytes 2G
```

---

## Step 20 — Security Configuration

- Create non-root Proxmox user  
- Assign roles  
- Enable Proxmox firewall  
- Enforce AD password and lockout policies  
- Restrict administrative access through groups and GPOs  
- Control file share access using groups  

---

## Step 21 — Automation

### Example Proxmox CLI Commands

```bash
qm list
qm snapshot <vmid> test
qm rollback <vmid> test
```

### Example PowerShell Tasks

- Create AD users  
- Create AD groups  
- Assign group membership  

---

## Validation Checklist

### Infrastructure

- Proxmox accessible  
- pfSense routing working  
- DHCP functioning  
- Network segmentation working  

### Linux

- Linux VM reachable via SSH  
- NGINX accessible from client  

### Windows / AD

- Domain Controller online  
- DNS resolves `northwind.local`  
- Client joined to domain  
- Domain login successful  

### Policy / Access

- GPOs applying correctly  
- Drive mapping works  
- File shares accessible based on group membership  

### Operations

- Backup and restore tested  
- Clone working correctly  
- Disk expansion verified  

---

## Common Pitfalls

- Wrong network bridge assignment  
- MAC address mismatch after cloning  
- Missing NAT configuration  
- DNS resolver issues behind NAT  
- Incorrect DNS handed to AD clients  
- Wrong subnet mask on server  
- Using folder path instead of share name for drive mapping  
- Assuming ping failure always means routing failure  

---

## Important Notes

### VM Restore / Clone Identity

When restoring VMs in Proxmox, ensure the **Unique** option is selected.

Failure to do so may result in:

- Duplicate system identity  
- Network issues  
- DHCP conflicts  

### Active Directory DNS Design

In AD environments, clients should use the Domain Controller as DNS.

Correct flow:

```text
Client → Domain Controller DNS → pfSense / upstream resolver → Internet
```

---

## Summary

This setup guide provides a complete process for building a segmented enterprise lab environment.

### Phase 1 established:

- Virtualization  
- Network segmentation  
- pfSense routing and firewalling  
- Linux services  
- Backup, restore, clone, and recovery scenarios  

### Phase 2 extends the environment with:

- Active Directory  
- DNS integration  
- Domain join  
- Group Policy  
- File services  
- Group-based access control  

---

Together, these stages create a realistic infrastructure lab that supports later expansion into cloud integration, hybrid identity, automation, monitoring, and advanced operations.