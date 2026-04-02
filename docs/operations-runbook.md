# Operations Runbook — Northwind Enterprise Lab

## Overview

This runbook provides operational procedures for managing and maintaining the Northwind Enterprise Lab.

It is designed for:

- Day-to-day operations  
- Incident response  
- Service validation  
- System maintenance  

The runbook focuses on repeatable actions and commands required to operate the environment efficiently.

---

## Environment Summary

Core components:

- Proxmox VE (virtualization platform)  
- pfSense (firewall and router)  
- Linux servers (Ubuntu with NGINX)  
- Windows client VM  

---

## Access Methods

| System | Access Method |
|------|-------------|
| Linux VMs | SSH |
| Windows VM | Console / RDP (future) |
| Proxmox | Web UI (port 8006) |
| pfSense | Web UI |

---

## Daily Checks

### Check VM Status

```bash
qm list

Verify:

All required VMs are running

---

Check System Resources

On Proxmox:

CPU usage
Memory usage
Disk usage

On Linux VM:
top


Check Disk Usage:
df -h

---

Service Management

Check NGINX Status:
systemctl status nginx


Restart NGINX
sudo systemctl restart nginx


Stop NGINX
sudo systemctl stop nginx


Start NGINX
sudo systemctl start nginx

---

Network Validation

Check IP Address
ip a


Check Routing
ip route


Test Connectivity (IP)
ping 8.8.8.8


Test DNS
ping google.com

---

Troubleshooting Workflow

Follow this order:

Check IP assignment
Verify interface status
Confirm default gateway
Test connectivity (IP)
Test DNS
Check firewall (pfSense)
Check service status

---


Backup Operations

Run Manual Backup

Proxmox GUI:
VM → Backup → Backup now


Verify Backup

Node → local → Backup
Confirm .vma.zst file exists


Restore Operations

Restore VM
Node → local → Backup
Select backup
Click Restore
Assign new VM ID


Validate Restored VM

systemctl status nginx

---

Snapshot Operations

Create Snapshot
qm snapshot <vmid> <name>


List Snapshots
qm listsnapshot <vmid>


Rollback Snapshot
qm rollback <vmid> <name>


Template and Cloning

Clone VM
Right-click template → Clone


Post-Clone Checks

Verify IP address
Check network interface
Confirm hostname

---

Storage Management

Check Disk Usage
df -h


Extend Disk (Linux)
sudo growpart /dev/sda 3
sudo pvresize /dev/sda3
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv

---

Incident Scenarios

Scenario: No IP Address

Actions:

Check interface:
ip a

Apply netplan:
sudo netplan apply

---

Scenario: No Internet Access

Actions:

Check gateway:
ip route

Verify pfSense NAT and firewall

---

Scenario: Service Down

Actions:

Check service:
systemctl status nginx

Restart:
sudo systemctl restart nginx

---

Scenario: Disk Full

Actions:

Check usage:
df -h

Remove unnecessary files
Extend disk

---

User Management (Proxmox)

Add User
Datacenter → Permissions → Users → Add

Assign Role
Datacenter → Permissions → Add → User Permission

---

## Security Checks
Ensure firewall enabled (Datacenter and Node)
Verify access rules
Avoid using root account for daily tasks

---

Automation

Snapshot Script Example:

```bash
#!/bin/bash

for vm in 102 103 104
do
  qm snapshot $vm auto-snap-$(date +%F)
```

---

Maintenance Tasks

Update Linux System
sudo apt update && sudo apt upgrade -y

Clean Package Cache
sudo apt clean

---

Key Principles

Always validate before and after changes
Use snapshots before major operations
Prefer SSH over console access
Follow structured troubleshooting
Maintain consistent naming and configuration

---

Summary

This runbook provides a structured approach to operating the Northwind Enterprise Lab.

It ensures:

Consistent operations
Faster troubleshooting
Reliable recovery
Alignment with real-world infrastructure practices