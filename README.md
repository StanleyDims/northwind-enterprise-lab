# Northwind Enterprise Lab

## Overview

This repository documents my hybrid enterprise infrastructure lab simulating a real-world environment.

The lab is built to demonstrate hands-on skills in:

- infrastructure  
- networking  
- system administration  
- virtualization  
- identity and access management  
- cloud (planned)  
- documentation  
- troubleshooting  

This lab demonstrates the design, deployment, and operation of a production-like environment using:

- Virtualization (Proxmox VE)  
- Network segmentation and firewalling (pfSense)  
- Active Directory and Group Policy  
- Linux and Windows systems  
- File services and access control  
- Backup and recovery strategies  
- Infrastructure automation and operational practices  

This lab reflects practical experience in building, troubleshooting, and managing enterprise-style systems.

---

## Lab Scenario

This lab simulates **Northwind Digital Services Ltd**, a mid-sized UK company with:

- 120 users  
- hybrid workforce  
- on-prem systems  
- remote workers  
- growing Azure footprint  
- Microsoft 365 collaboration  
- Windows and Linux workloads  

---

## Core Components

- Proxmox VE (Hypervisor)  
- pfSense (Firewall and Router)  
- Windows Server (Active Directory, DNS, File Services)  
- Linux Servers (Ubuntu with NGINX)  
- Windows Client (Domain Joined)  

---

## Network Segments

| Network | Subnet | Purpose |
|--------|--------|--------|
| Server | 10.10.20.0/24 | Internal services (AD, File Server, Linux workloads) |
| Client | 10.10.30.0/24 | User devices (domain-joined clients) |
| DMZ | 10.10.40.0/24 | Public-facing services (planned) |
| WAN | 192.168.x.x | External network |

---

## Key Features

### Phase 1 – Infrastructure Foundation

- Network segmentation using virtual bridges  
- Centralized routing and NAT via pfSense  
- SSH-based remote administration  
- NGINX service deployment and validation  
- Snapshot and backup strategy implementation  
- VM templating and cloning  
- Disk management using LVM  
- Resource monitoring and stress testing  
- Basic automation using Proxmox CLI  

### Phase 2 – Identity, Policy & Access

- Active Directory Domain Services deployment  
- DNS integrated with AD for internal name resolution  
- DHCP (pfSense) configured to distribute DC as DNS  
- OU structure and role-based access control (RBAC)  
- User and group management via PowerShell  
- Group Policy implementation:
  - password and account lockout policies  
  - user restrictions (Control Panel for HR)  
  - RDP access control for administrators  
  - application restriction for non-admin users  
- File services:
  - shared folders on NW-FS01  
  - NTFS and share permissions  
  - group-based access control  
- Drive mapping using GPO with targeted access  

---

## DNS and Network Flow (Key Design)

The environment uses a hybrid DNS approach:

```text
Client → Domain Controller (DNS) → pfSense → Internet
```

- Clients receive DNS via DHCP (pfSense)  
- Domain Controller handles internal AD resolution  
- External queries are forwarded upstream  

This design reflects real enterprise environments and ensures proper Active Directory functionality.

---

## Documentation

Detailed documentation is available in the `docs/` directory:

- Architecture Design  
- Network Design  
- Identity and Access Design  
- Group Policy and Access Control  
- File Services and Permissions  
- DNS and DHCP Integration  
- VM Inventory  
- Storage Design  
- Backup and Recovery Strategy  
- Security Model  
- Troubleshooting Guide  
- Operations Runbook  
- Lab Setup Guide  
- Lessons Learned  

---

## Project Structure

```text
docs/                         Core design and operational documentation
phase-1-foundation/           Infrastructure build (Proxmox, networking)
phase-2-active-directory/     Identity, GPO, and file services implementation
```

---

## Key Skills Demonstrated

- Infrastructure design and virtualization  
- Network segmentation and firewall configuration  
- Active Directory and identity management  
- DNS and DHCP integration  
- Group Policy design and implementation  
- Role-based access control (RBAC)  
- File services and permission management  
- Linux system administration  
- Troubleshooting across network, system, and identity layers  
- Backup and disaster recovery  
- Storage management and LVM  
- Automation using CLI and PowerShell  
- Technical documentation  

---

## Real-World Scenarios Simulated

- Domain join and authentication  
- DNS misconfiguration and resolution troubleshooting  
- Subnet misconfiguration and connectivity issues  
- Inter-subnet routing validation  
- Group Policy application and troubleshooting  
- File share access issues and permission conflicts  
- Drive mapping via GPO  
- Service deployment and validation  
- System failure and recovery  
- Disk exhaustion and expansion  
- VM cloning and scaling  

---

## Future Phases

Planned enhancements include:

- Azure cloud infrastructure deployment  
- Hybrid identity (on-prem AD + Entra ID)  
- Infrastructure as Code (Terraform)  
- Containerization (Docker/Kubernetes)  
- Monitoring and logging solutions  
- Backup and recovery automation  

---

## Summary

This project demonstrates practical, real-world infrastructure engineering skills across networking, identity, system administration, and operations.

It reflects how enterprise environments are designed, implemented, and troubleshot, and serves as a strong foundation for cloud, DevOps, and solution architecture roles.