# Northwind Enterprise Lab
# Overview
This repository documents my hybrid enterprise infrastructure lab simulating a real-world environment.
The lab is built to demonstrate hands-on skills in:
- infrastructure
- networking
- system administration
- virtualization
- cloud
- documentation
- troubleshooting

This lab demonstrates the infrastructural design, deployment, and operation using:

- Virtualization (Proxmox VE)  
- Network segmentation and firewalling (pfSense)  
- Linux and Windows systems  
- Backup and recovery strategies  
- Infrastructure automation and operational practices  

This lab reflects practical experience in building and managing production-like systems.


## Lab Scenario
This lab simulates Northwind Digital Services Ltd, a mid-sized UK company with:
- 120 users
- hybrid workforce
- on-prem systems
- remote workers
- growing Azure footprint
- Microsoft 365 collaboration
- Windows and Linux workloads

### Core Components

- Proxmox VE (Hypervisor)  
- pfSense (Firewall and Router)  
- Linux Servers (Ubuntu with NGINX)  
- Windows Client  

### Network Segments

| Network | Subnet | Purpose |
|--------|--------|--------|
| Server | 10.10.20.0/24 | Internal services |
| Client | 10.10.30.0/24 | User devices |
| DMZ | 10.10.40.0/24 | Public-facing services |
| WAN | 192.168.x.x | External network |

---

## Key Features

- Network segmentation using virtual bridges  
- Centralized routing and NAT via pfSense  
- SSH-based remote administration  
- NGINX service deployment and validation  
- Snapshot and backup strategy implementation  
- VM templating and cloning  
- Disk management using LVM  
- Resource monitoring and stress testing  
- Basic automation using Proxmox CLI  

---

## Documentation

Detailed documentation is available in the `docs/` directory:

- Architecture Design  
- Network Design  
- IP Addressing Plan  
- VM Inventory  
- Storage Design  
- Backup and Recovery Strategy  
- Snapshot and Template Strategy  
- Security Model  
- Troubleshooting Guide  
- Operations Runbook  
- Lab Setup Guide  
- Lessons Learned  

---

## Project Structure

northwind-enterprise-lab/
│
├── README.md
│
├── docs/
│ ├── architecture.md
│ ├── network-design.md
│ ├── ip-plan.md
│ ├── vm-inventory.md
│ ├── storage-design.md
│ ├── backup-and-recovery.md
│ ├── snapshot-and-template-strategy.md
│ ├── security-model.md
│ ├── troubleshooting-guide.md
│ ├── operations-runbook.md
│ ├── lab-setup-guide.md
│ └── lessons-learned.md
│
├── phase-1-foundation/
│ └── README.md
│
├── phase-2-active-directory/
│ └── README.md


---

## Key Skills Demonstrated

- Infrastructure design and virtualization  
- Network segmentation and firewall configuration  
- Linux system administration  
- Troubleshooting across multiple layers  
- Backup and disaster recovery  
- Storage management and LVM  
- Resource monitoring and performance tuning  
- Automation using CLI tools  
- Technical documentation  

---

## Real-World Scenarios Simulated

- Service deployment and validation  
- System failure and recovery  
- Disk exhaustion and expansion  
- VM cloning and scaling  
- Network misconfiguration troubleshooting  
- DNS and NAT-related issues  

---

## Future Phases

Planned enhancements include:

- Active Directory deployment (Windows Server)  
- Centralized authentication and group policies  
- Azure cloud integration  
- Infrastructure as Code (Terraform)  
- Containerization (Kubernetes)  
- Monitoring and logging solutions  

---

## Summary

This project demonstrates practical, real-world infrastructure engineering skills, including system design, troubleshooting, and operational management.

It is structured to reflect enterprise environments and serves as a foundation for advanced topics in cloud, DevOps, and solution architecture.