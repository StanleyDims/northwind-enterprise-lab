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
| 103 | NW-LX01-RESTORE | Restored Linux Server | Ubuntu Server | Server | DHCP | Running |
| 104 | NW-LX02 | Cloned Linux Server | Ubuntu Server | Server | DHCP | Running |
| 105 | NW-WKS01 | Windows Client | Windows | Client | DHCP | Running |

---

## Detailed VM Descriptions

### pfSense (VM ID: 100)

**Role:**  
Firewall, router, and network control layer

**Responsibilities:**
- Routing between networks  
- NAT (internal to internet)  
- DHCP services  
- Firewall enforcement  

**Interfaces:**
- WAN → vmbr0  
- LAN (Server) → vmbr1  
- OPT1 (Client) → vmbr2  
- OPT2 (DMZ) → vmbr3  

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

### NW-LX01-RESTORE (VM ID: 103)

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
- Test access to servers and services  

**Tools Used:**
- MobaXterm (SSH client)  
- Web browser (NGINX access)  

---

## Network Placement Summary

| VM Name | Network | Subnet |
|--------|--------|--------|
| pfSense | All | Multiple |
| NW-LX01 / LX02 / RESTORE | Server | 10.10.20.0/24 |
| NW-WKS01 | Client | 10.10.30.0/24 |

---

## Services Overview

| Service | VM | Purpose |
|--------|----|--------|
| NGINX | NW-LX01 / LX02 / RESTORE | Web service simulation |
| SSH | Linux VMs | Remote administration |
| DHCP | pfSense | IP assignment |
| DNS | pfSense | Name resolution |
| NAT | pfSense | Internet access |

---

## Operational Use Cases

The VM environment supports the following scenarios:

- Remote administration via SSH  
- Service deployment and validation (NGINX)  
- Backup and restore testing  
- VM cloning and scaling  
- Network troubleshooting  
- Migration and failover simulation  

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
| DNS delay | Initial lookup latency | Verified resolver |

---

## Design Principles

- Separate roles across VMs  
- Use templates for consistency  
- Validate recovery processes  
- Maintain clear naming conventions  
- Align infrastructure with real-world scenarios  

---

## Future Additions

Planned VMs for next phases:

- NW-DC01 → Domain Controller  
- NW-FS01 → File Server  
- Additional application servers  
- Monitoring/logging server  

---

## Summary

This VM inventory represents a structured and scalable environment that simulates enterprise infrastructure.

It demonstrates:
- Deployment  
- Management  
- Recovery  
- Troubleshooting  
- Scaling  

and forms the foundation for further phases such as Active Directory and cloud integration.