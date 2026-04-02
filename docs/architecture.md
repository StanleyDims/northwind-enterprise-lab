# Architecture Overview — Northwind Enterprise Lab

## Overview

This lab simulates a real-world hybrid enterprise infrastructure for a mid-sized organization (Northwind Digital Services Ltd).

The environment is designed to reflect:

- On-premises infrastructure  
- Network segmentation and security  
- Remote administration workflows  
- Service hosting and troubleshooting  
- Backup, recovery, and operational resilience  

The architecture is built on Proxmox VE, with a virtualized network controlled by pfSense, and multiple VMs representing servers and clients.

---

## Core Components

### Virtualization Layer
- Platform: Proxmox VE  
- Storage:
  - local → ISO images, backups  
  - local-lvm → VM disks (thin provisioned)  

Capabilities:
- VM lifecycle management  
- Snapshots  
- Backup scheduling  
- Resource allocation  

---

### Network and Security Layer
- Firewall: pfSense  

Responsibilities:
- Routing between networks  
- NAT (internal to internet)  
- DHCP per network  
- Firewall rule enforcement  

---

## Network Design

| Network | Subnet | Purpose |
|--------|--------|--------|
| Server | 10.10.20.0/24 | Internal servers |
| Client | 10.10.30.0/24 | User devices |
| DMZ | 10.10.40.0/24 | Public-facing services |
| WAN | 192.168.x.x | External network |

---

## Traffic Flow

### Internal communication
Client → pfSense → Server

### Internet access
Server/Client → pfSense (NAT) → Internet

### Service access
Client → NGINX (Linux Server)

---

## Virtual Machines

| VM Name | Role | Network |
|--------|------|--------|
| NW-LX01 | Linux Server (NGINX) | Server |
| NW-WKS01 | Windows Client | Client |
| NW-LX02 | Cloned Linux Server | Server |
| pfSense | Firewall/Router | WAN + all internal |

---

## Access Model

| Access Type | Method |
|------------|--------|
| Linux administration | SSH |
| Windows administration | RDP (future) |
| Hypervisor | Proxmox Web UI |
| Firewall | pfSense Web UI |

Console access is only used for recovery scenarios.

---

## Service Layer

### NGINX (Linux Server)
Used to simulate:
- Web service hosting  
- Application availability  
- Troubleshooting scenarios  

---

## Storage Design

- Thin provisioning on local-lvm  
- Dynamic disk expansion supported  
- LVM used inside Linux VM for flexibility  

Capabilities:
- Resize disk at hypervisor level  
- Extend filesystem without downtime  

---

## Resource Management

Monitored via:
- Proxmox dashboard  
- Linux tools (top, htop, stress)  

Test scenarios:
- CPU saturation  
- Memory pressure  
- Disk I/O stress  

---

## Backup and Recovery Strategy

### Snapshots
- Used for short-term rollback  
- Taken before system changes  

### Backups
- Scheduled via Proxmox backup jobs  
- Stored on local storage  
- Compressed using zstd  

### Recovery Tested
- Full VM restore to new instance  
- Service validation post-restore (NGINX)  

---

## High Availability Simulation

### Migration (planned)
- Service moved from primary VM to cloned VM  

### Failover (unplanned)
- Primary VM stopped  
- Backup VM used to restore service  

Limitations:
- Manual failover (no load balancer yet)  

---

## Security Model

### Network Security
- Segmented networks (Server, Client, DMZ)  
- Firewall rules enforced via pfSense  

### Host Security
- Proxmox RBAC (non-root user)  
- Firewall enabled at node level  

### Access Control
- SSH for Linux  
- Controlled administrative access  

---

## Automation

Using Proxmox CLI:

- VM listing (qm list)  
- Snapshot creation (qm snapshot)  
- Batch operations via scripts  

---

## Monitoring and Observability (Basic)

- System metrics via Proxmox  
- Process monitoring via Linux tools  
- Manual health checks (NGINX availability)  

---

## Scalability and Future Design

Planned enhancements:

- Active Directory (Phase 2)  
- Azure integration  
- Terraform automation  
- Kubernetes workloads  
- Centralized logging  

---

## Key Skills Demonstrated

- Network segmentation  
- Firewall configuration (pfSense)  
- Linux administration (NGINX, SSH)  
- VM lifecycle management  
- Backup and recovery  
- Troubleshooting (DNS, routing, DHCP)  
- Resource optimization  
- Infrastructure automation basics  