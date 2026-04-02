# Phase 1 — Foundation (Infrastructure & Networking)

## Overview

Phase 1 focuses on building the foundational infrastructure for the Northwind Enterprise Lab.

The objective was to design and implement a production-like environment that includes:

- Virtualization platform  
- Segmented network architecture  
- Firewall and routing  
- Server and client systems  
- Core services  
- Backup and recovery mechanisms  

This phase establishes the base upon which future phases (Active Directory, cloud, automation) will be built.

---

## Architecture Diagram

graph TD

    Internet["Internet"]

    subgraph Proxmox["Proxmox VE Host"]

        pfSense["pfSense Firewall / Router<br/>WAN: vmbr0<br/>LAN: vmbr1<br/>CLIENT: vmbr2<br/>DMZ: vmbr3"]

        subgraph ServerNet["Server Network (10.10.20.0/24)"]
            LX01["NW-LX01<br/>Ubuntu + NGINX"]
            LX02["NW-LX02<br/>Cloned Server"]
            LXRESTORE["NW-LX03-RESTORE<br/>Recovered VM"]
        end

        subgraph ClientNet["Client Network (10.10.30.0/24)"]
            WKS01["NW-WKS01<br/>Windows Client"]
        end

        subgraph DMZNet["DMZ Network (10.10.40.0/24)"]
            DMZ1["Future DMZ Services"]
        end
    end

    Internet -->|WAN| pfSense

    pfSense -->|LAN| LX01TEMPLATE
    pfSense -->|LAN| LX02CLONED
    pfSense -->|LAN| LX03RESTORE
    pfSense -->|CLIENT| WKS01
    pfSense -->|DMZ| DMZ1

    WKS01 -->|HTTP / SSH| LX01
    pfSense -->|NAT| Internet

---

## Objectives

- Deploy Proxmox VE as the hypervisor  
- Implement network segmentation using virtual bridges  
- Configure pfSense as a firewall and router  
- Deploy Linux and Windows virtual machines  
- Enable secure remote access (SSH)  
- Deploy and validate a web service (NGINX)  
- Implement backup and snapshot strategies  
- Practice real-world troubleshooting scenarios  

---

## Architecture Implemented

### Core Components

- Proxmox VE (virtualization platform)  
- pfSense (firewall and router)  
- Ubuntu Server (Linux server)  
- Windows VM (client workstation)  

---

### Network Segmentation

| Network | Subnet | Purpose |
|--------|--------|--------|
| Server | 10.10.20.0/24 | Internal services |
| Client | 10.10.30.0/24 | User devices |
| DMZ | 10.10.40.0/24 | Public-facing services |
| WAN | 192.168.x.x | External network |

---

### Network Design

- Virtual bridges created in Proxmox  
- pfSense used as central routing and firewall layer  
- NAT configured for internet access  
- DHCP configured per network 

graph LR

    Internet["Internet"]
    pfSense["FW01 (pfSense)<br/>Firewall / Router"]

    subgraph ClientNet["Client Network (10.10.30.0/24)"]
        WKS01["NW-WKS01<br/>Windows Client"]
    end

    subgraph ServerNet["Server Network (10.10.20.0/24)"]
        LX02["NW-LX02-CLONED<br/>Ubuntu + NGINX"]
        LX03["NW-LX03-RESTORE<br/>Recovered VM"]
    end

    subgraph DMZNet["DMZ Network (10.10.40.0/24)"]
        DMZ1["Future DMZ Services"]
    end

    WKS01 <-->|HTTP / SSH| pfSense
    pfSense <-->|HTTP / SSH| LX02
    pfSense <-->|HTTP / SSH| LX03

    pfSense <-->|Restricted DMZ traffic| DMZ1

    pfSense <-->|Outbound NAT / Replies| Internet

---

## Virtual Machines Deployed

| VM Name | Role |
|--------|------|
| pfSense | Firewall and router |
| NW-LX01 | Linux server (NGINX) |
| NW-WKS01 | Windows client |
| NW-LX03-RESTORE | Restored backup instance |
| NW-LX02-CLONED | Cloned Linux server |

---

## Key Implementations

### 1. Virtualization Platform

- Installed and configured Proxmox VE  
- Created and managed virtual machines  
- Configured storage (local and local-lvm)  

---

### 2. Network and Firewall

- Designed segmented network architecture  
- Configured pfSense interfaces and routing  
- Implemented firewall rules  
- Enabled NAT for internet access  

---

### 3. Linux Server Deployment

- Installed Ubuntu Server  
- Configured SSH access  
- Deployed NGINX  
- Verified service accessibility from client  

---

### 4. Backup and Recovery

- Configured scheduled backups  
- Performed manual backups  
- Restored VM from backup  
- Validated service after recovery  

---

### 5. Snapshot and Template Strategy

- Created snapshots before changes  
- Converted VM to template  
- Cloned new VMs from template  
- Resolved post-clone network issues  

---

### 6. Storage Management

- Implemented thin provisioning  
- Simulated disk exhaustion  
- Extended disk using LVM  
- Verified filesystem expansion  

---

### 7. Resource Management

- Monitored CPU, memory, and disk usage  
- Simulated load using stress tool  
- Observed system performance under pressure  

---

### 8. Security Basics

- Implemented network segmentation  
- Configured firewall rules  
- Created non-root Proxmox user  
- Enabled Proxmox firewall  

---

### 9. Automation

- Used Proxmox CLI (`qm`)  
- Automated snapshot creation using scripts  

---

## Challenges and Troubleshooting

Several real-world issues were encountered and resolved:

- No IP after cloning (MAC mismatch in netplan)  
- Interface not initialized  
- No internet access due to NAT and routing  
- DNS issues in NAT environment (pfSense forwarding mode)  
- Service accessibility issues  
- Disk space exhaustion  

A structured troubleshooting approach was used:

1. Check IP assignment  
2. Verify interface  
3. Confirm routing  
4. Test connectivity (IP first, then DNS)  
5. Check firewall and NAT  
6. Validate services  

---

## Key Lessons

- Infrastructure is more than VM deployment  
- Network segmentation improves control and security  
- Firewall and NAT are central to connectivity  
- DNS issues must be distinguished from routing issues  
- Cloning introduces hidden configuration problems  
- Backups must be tested, not assumed  
- Documentation is essential  

---

## Outcome

At the end of Phase 1:

- A fully functional segmented network was deployed  
- Services were accessible across networks  
- Backup and recovery processes were validated  
- Multiple real-world troubleshooting scenarios were resolved  
- The environment became stable and reproducible  

---

## What This Phase Demonstrates

- Infrastructure design and implementation  
- Networking and firewall configuration  
- Linux administration and service deployment  
- Troubleshooting across multiple layers  
- Backup and recovery strategy  
- Storage and resource management  
- Operational thinking  

---

## Next Phase

Phase 2 will introduce:

- Active Directory (Windows Server)  
- Centralized authentication  
- Domain-based management  
- DNS and Group Policy  

This will extend the lab into a full enterprise identity environment.

---

## Related Documentation

Refer to the `docs/` directory for detailed technical documentation:

- Architecture design  
- Network design  
- IP addressing plan  
- Security model  
- Troubleshooting guide  
- Operations runbook  
- Storage and backup strategies  

---

## Summary

Phase 1 establishes a strong foundation for enterprise infrastructure by combining virtualization, networking, security, and operations.

It provides practical experience aligned with real-world IT environments and prepares for more advanced implementations in subsequent phases.