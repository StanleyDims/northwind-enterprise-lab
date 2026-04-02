# Lab Setup Guide — Northwind Enterprise Lab

## Overview

This document provides a step-by-step guide to building the Northwind Enterprise Lab from scratch.

The setup simulates a real-world enterprise environment using:

- Proxmox VE (virtualization platform)  
- pfSense (firewall and router)  
- Linux and Windows virtual machines  
- Segmented networks  

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
- Windows ISO  

---

## Step 1 — Install Proxmox VE

1. Download Proxmox VE ISO  
2. Create bootable USB  
3. Boot server from USB  
4. Follow installation wizard:
   - Select disk  
   - Set root password  
   - Configure network  

5. After installation:
   - Access web UI:
     ```
     https://<proxmox-ip>:8006
     ``` id="lab_proxmox_url"

---

## Step 2 — Configure Proxmox Storage

Default storage:

| Storage | Purpose |
|--------|--------|
| local | ISO images, backups |
| local-lvm | VM disks |

Upload ISOs:
- Node → local → ISO Images → Upload  

---

## Step 3 — Create Network Bridges

Go to:
- Node → Network  

Create:

| Bridge | Purpose |
|--------|--------|
| vmbr0 | WAN (physical NIC) |
| vmbr1 | Server network |
| vmbr2 | Client network |
| vmbr3 | DMZ network |

Apply configuration and restart network if required.

---

## Step 4 — Deploy pfSense

### Create VM

- CPU: 2 cores  
- RAM: 2 GB  
- Disk: 20 GB  
- Network:
  - Adapter 1 → vmbr0 (WAN)  
  - Adapter 2 → vmbr1 (LAN)  
  - Adapter 3 → vmbr2 (CLIENT)  
  - Adapter 4 → vmbr3 (DMZ)  

---

### Install pfSense

- Boot from ISO  
- Follow installation  
- Assign interfaces  

---

### Configure Interfaces

| Interface | IP |
|----------|----|
| LAN | 10.10.20.1 |
| CLIENT | 10.10.30.1 |
| DMZ | 10.10.40.1 |

---

### Enable Services

- DHCP for each network  
- DNS Resolver (enable forwarding mode if behind NAT)  

---

### Configure Firewall

- Allow LAN → any  
- Allow CLIENT → any  
- Allow DMZ → WAN only  

---

## Step 5 — Create Linux VM (NW-LX01)

### VM Configuration

- CPU: 2 cores  
- RAM: 4 GB  
- Disk: 30–64 GB  
- Network: vmbr1  

---

### Install Ubuntu Server

- Boot from ISO  
- Configure:
  - Username  
  - Hostname  
  - Install OpenSSH  

---

### Verify Network

```bash
ip a
ping 8.8.8.8
ping google.com
``` id="lab_linux_test"

---

## Step 6 — Install NGINX

```bash
sudo apt update
sudo apt install nginx -y
``` id="lab_nginx_install"

Verify:

```bash
systemctl status nginx
``` id="lab_nginx_status"

---

## Step 7 — Create Windows VM (NW-WKS01)

### VM Configuration

- CPU: 2 cores  
- RAM: 4 GB  
- Disk: 30–64 GB  
- Network: vmbr2  

---

### Install Windows

- Attach Windows ISO  
- Install OS  
- Install VirtIO drivers if required  

---

### Test Connectivity

From Windows:

- Ping Linux server  
- Access NGINX via browser  

---

## Step 8 — Configure Backup

1. Datacenter → Backup → Add  
2. Configure:
   - VM: NW-LX01  
   - Schedule: daily  
   - Mode: snapshot  
   - Compression: zstd  

---

### Test Backup

- Run backup manually  
- Verify file exists  

---

### Test Restore

- Restore to new VM  
- Validate service  

---

## Step 9 — Configure Template and Clone

1. Prepare Linux VM  
2. Convert to template  
3. Clone new VM  

---

### Post-Clone Fix

- Verify network interface  
- Remove MAC binding if necessary  
- Confirm IP assignment  

---

## Step 10 — Storage Lab

### Simulate Disk Usage

```bash
fallocate -l 5G bigfile
``` id="lab_storage_test"

---

### Resize Disk

- Increase disk in Proxmox  
- Extend LVM inside Linux  

---

## Step 11 — Resource Management

### Install stress tool

```bash
sudo apt install stress -y
``` id="lab_stress_install"

---

### Simulate load

```bash
stress --cpu 2
stress --vm 1 --vm-bytes 2G
``` id="lab_stress_run"

---

## Step 12 — Security Configuration

- Create non-root Proxmox user  
- Assign roles  
- Enable Proxmox firewall  

---

## Step 13 — Automation

### Example CLI commands

```bash
qm list
qm snapshot <vmid> test
qm rollback <vmid> test
``` id="lab_cli"

---

## Validation Checklist

- Proxmox accessible  
- pfSense routing working  
- DHCP functioning  
- Linux VM reachable via SSH  
- NGINX accessible from client  
- Backup and restore tested  
- Clone working correctly  
- Disk expansion verified  

---

## Common Pitfalls

- Wrong network bridge assignment  
- MAC address mismatch after cloning  
- Missing NAT configuration  
- DNS resolver issues behind NAT  
- Interface not initialized  

---

### Important Note

When restoring VMs in Proxmox, ensure the "Unique" option is selected.

Failure to do so may result in:
- Duplicate system identity  
- Network issues  
- DHCP conflicts  

---

## Summary

This setup guide provides a complete process for building a segmented enterprise lab environment.

It ensures:

- Functional infrastructure  
- Realistic networking  
- Service deployment  
- Operational readiness  

The lab forms the foundation for advanced topics such as Active Directory, cloud integration, and automation.