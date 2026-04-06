# VM Inventory — Northwind Enterprise Lab

## Overview

This document provides an inventory of all virtual machines deployed in the Northwind Enterprise Lab.

It includes:
- VM identification (ID, name)
- Role and purpose
- Network placement
- Key services
- Status and notes

This inventory is used for:
- Operational visibility  
- Troubleshooting  
- Change management  
- Documentation for interviews and audits  

---

## Virtual Machine Inventory

| VM ID | Name | Role | OS | Network | IP Address | Status |
|------|------|------|----|--------|-----------|--------|
| 100 | pfSense | Firewall / Router | pfSense | WAN + Server + Client + DMZ | Multiple | Running |
| 102 | NW-LX01 (Template) | Linux Server Template | Ubuntu Server | Server | N/A | Template |
| 103 | NW-LX03 | Restored Linux Server | Ubuntu Server | Server | DHCP | Running |
| 104 | NW-LX02 | Cloned Linux Server | Ubuntu Server | Server | DHCP | Running |
| 105 | NW-WKS01 | Windows Client (Domain Joined) | Windows | Client | DHCP | Running |
| 106 | NW-DC01 | Domain Controller (AD DS + DNS) | Windows Server | Server | Static | Running |
| 107 | NW-FS01 | File Server (SMB Shares) | Windows Server | Server | Static | Running |

---

## Detailed VM Descriptions

### pfSense (VM ID: 100)

**Role:**  
Firewall, router, and network control layer

**Responsibilities:**
- Routing between networks  
- NAT (internal to internet)  
- DHCP services (Client network)  
- Firewall enforcement  

**Interfaces:**
- WAN → vmbr0  
- Server → vmbr1  
- Client → vmbr2  
- DMZ → vmbr3  

---

### NW-LX01 (Template) (VM ID: 102)

**Role:**  
Base Linux server template

**OS:**  
Ubuntu Server LTS

**Purpose:**
- Standardized image for cloning  
- Preconfigured with:
  - SSH access  
  - NGINX installed  
  - Updated packages  

**Notes:**
- Converted to template  
- Not runnable  
- Used to deploy new servers  

---

### NW-LX03 (VM ID: 103)

**Role:**  
Restored backup instance

**OS:**  
Ubuntu Server LTS

**Purpose:**
- Validate backup and recovery process  
- Simulate disaster recovery  

**Services:**
- NGINX  

**Notes:**
- Created from Proxmox backup  
- Used in failover simulation  

---

### NW-LX02 (VM ID: 104)

**Role:**  
Cloned Linux server

**OS:**  
Ubuntu Server LTS

**Purpose:**
- Demonstrate template cloning  
- Simulate scaling and migration  

**Services:**
- NGINX  

**Notes:**
- Required network fix after cloning (MAC mismatch)  
- Used for migration and failover testing  

---

### NW-WKS01 (VM ID: 105)

**Role:**  
Client workstation

**OS:**  
Windows

**Purpose:**
- Simulate user device  
- Test domain authentication  
- Validate GPO and access control  

**Key Functions:**
- Domain joined to `northwind.local`  
- Receives Group Policy  
- Accesses file shares via mapped drives  

---

### NW-DC01 (VM ID: 106) 

**Role:**  
Domain Controller

**OS:**  
Windows Server

**Services:**
- Active Directory Domain Services (AD DS)  
- DNS (AD-integrated)  

**Responsibilities:**
- Centralized authentication  
- Identity management  
- DNS resolution for internal domain  

**Configuration:**
- Static IP assigned  
- DNS points to itself  
- Domain: `northwind.local`  

---

### NW-FS01 (VM ID: 107) 

**Role:**  
File Server

**OS:**  
Windows Server

**Services:**
- SMB file shares  

**Responsibilities:**
- Centralized file storage  
- Department-based access control  

**Shares:**
- `\\NW-FS01\Finance`  
- `\\NW-FS01\HR`  

**Access Model:**
- Controlled via AD security groups  

---

## Network Placement Summary

| VM Name | Network | Subnet |
|--------|--------|--------|
| pfSense | All | Multiple |
| NW-DC01 / NW-FS01 | Server | 10.10.20.0/24 |
| NW-LX02 / NW-LX03 | Server | 10.10.20.0/24 |
| NW-WKS01 | Client | 10.10.30.0/24 |

---

## Services Overview

| Service | VM | Purpose |
|--------|----|--------|
| AD DS | NW-DC01 | Identity management |
| DNS | NW-DC01 | Internal name resolution |
| DHCP | pfSense | IP assignment (Client network) |
| SMB | NW-FS01 | File sharing |
| NGINX | Linux VMs | Web service simulation |
| SSH | Linux VMs | Remote administration |
| NAT | pfSense | Internet access |

---

## Operational Use Cases

The VM environment supports the following scenarios:

- Domain authentication and login  
- Group Policy enforcement  
- File access via group-based permissions  
- Remote administration (SSH / RDP)  
- Backup and restore validation  
- VM cloning and scaling  
- Network and DNS troubleshooting  

---

## Lifecycle Management

| Stage | Action |
|------|--------|
| Creation | VM built from ISO |
| Configuration | OS setup and service installation |
| Template | Converted to reusable image |
| Clone | New VM created from template |
| Backup | Scheduled and manual backups |
| Restore | VM recovered from backup |

---

## Issues Encountered and Resolved

| Issue | Cause | Resolution |
|------|------|-----------|
| No IP after cloning | MAC mismatch in netplan | Updated or removed MAC binding |
| No internet access | NAT / routing delay | Verified pfSense and routing |
| Interface down | Network not initialized | Brought interface up |
| DNS timeout from client | DNS pointing to pfSense | Updated DHCP to use DC as DNS |
| Wrong subnet mask | Misconfiguration | Corrected to 255.255.255.0 |
| Same machine ID after restore | Unique option not selected | Enabled unique option during restore |

---

## Design Principles

- Separate roles across VMs  
- Use templates for consistency  
- Centralize identity and access  
- Enforce least privilege  
- Validate recovery processes  
- Maintain clear naming conventions  
- Align infrastructure with real-world enterprise design  

---

## Summary

This VM inventory represents a structured and evolving environment that simulates enterprise infrastructure.

Phase 1 established:
- Network segmentation  
- Firewall control  
- Virtualization foundation  

Phase 2 extends the environment with:
- Active Directory  
- Centralized authentication  
- File services  
- Policy-based management  

Together, they demonstrate a layered and scalable infrastructure design aligned with real-world enterprise environments.