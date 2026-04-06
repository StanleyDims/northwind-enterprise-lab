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

### Core components:

- Proxmox VE (virtualization platform)  
- pfSense (firewall, router, DHCP)  
- Active Directory Domain Controller (NW-DC01)  
- File Server (NW-FS01)  
- Linux servers (Ubuntu with NGINX)  
- Windows domain-joined client  

---

## Access Methods

| System | Access Method |
|------|-------------|
| Linux VMs | SSH |
| Windows Client | Domain Login / RDP |
| Domain Controller | Console / RDP |
| File Server | Console / RDP |
| Proxmox | Web UI (port 8006) |
| pfSense | Web UI |

---

## Daily Checks

### Check VM Status

```bash
qm list
```
Verify:

All required VMs are running

## Check System Resources

## Proxmox:
CPU usage
Memory usage
Disk usage

## Linux VM:
```bash
top
df -h
```

## Check Domain Services (NEW)

On NW-DC01:
```powershell
Get-Service adws, dns
```

Verify:

Active Directory services running
DNS service running

## Service Management

## Linux (NGINX)
```bash
systemctl status nginx
sudo systemctl restart nginx
sudo systemctl stop nginx
sudo systemctl start nginx
```

## Windows Services (AD / DNS)
```powershell
Get-Service dns
Get-Service netlogon
```

## Active Directory Operations

## Create User
```powershell
New-ADUser -Name "John IT" -SamAccountName "john.it" -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) -Enabled $true
```

## Create Group
```powershell
New-ADGroup -Name "IT_Admins" -GroupScope Global -GroupCategory Security
```

## Add User to Group
```powershell
Add-ADGroupMember -Identity "IT_Admins" -Members "john.it"
```

## Verify User
```powershell
Get-ADUser -Filter *
```

## Group Policy Operations

## Force Policy Update (Client)
```cmd
gpupdate /force
```

## Verify GPO Application
```cmd
gpresult /r
```

Detailed report:
```cmd
gpresult /h C:\temp\gpo-report.html
```

## File Services Operations

## Test Share Access
```cmd
\\NW-FS01\Finance
```

## Map Drive (Manual Test)
```cmd
net use F: \\NW-FS01\Finance
```

## Verify Permissions
Confirm user is in correct security group
Check NTFS + share permissions

## Network Validation

## Check IP
```bash
ip a
```
or
```cmd
ipconfig
```

## Check Routing
```bash
ip route
```

## Test Connectivity
```bash
ping 10.10.20.102   # DC
ping 10.10.20.x     # Servers
```

## Test DNS (IMPORTANT)
```cmd
nslookup northwind.local
nslookup google.com
```

## Troubleshooting Workflow

Follow this order:

1. Check IP assignment
2. Verify interface status
3. Confirm default gateway
4. Verify DNS (must point to DC)
5. Test connectivity (IP)
6. Test DNS resolution
7. Check firewall (pfSense)
8. Check service status

## Backup Operations

## Run Manual Backup

Proxmox GUI:

VM → Backup → Backup now

## Verify Backup
Node → local → Backup
Confirm .vma.zst file exists

## Restore VM
Node → local → Backup
Select backup → Restore
Assign new VM ID

## Validate Restored VM
```bash
systemctl status nginx
```

## Snapshot Operations
```bash
qm snapshot <vmid> <name>
qm listsnapshot <vmid>
qm rollback <vmid> <name>
```

## Template and Cloning
Right-click template → Clone

## Post-Clone Checks
Verify IP address
Check network interface
Confirm hostname

## Storage Management
```bash
df -h
```

Extend disk:

```bash
sudo growpart /dev/sda 3
sudo pvresize /dev/sda3
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

## Incident Scenarios

## Scenario: DNS Not Resolving 🔥

Actions:

```bash
ipconfig
nslookup northwind.local
```

Check:

DNS server is DC
DC reachable

## Scenario: Cannot Join Domain

Actions:

Verify DNS → must be DC
Check connectivity to DC
Check time sync

## Scenario: Cannot Access File Share

Actions:

Check group membership
Verify permissions
Test manual access

## Scenario: No Internet Access
```bash
ip route
```
Check pfSense NAT + firewall

## Scenario: Service Down
```bash
systemctl status nginx
sudo systemctl restart nginx
```

## Scenario: Disk Full
```bash
df -h
```

## Security Checks
Ensure firewall enabled (Proxmox + pfSense)
Verify GPO enforcement
Confirm least privilege access
Avoid using root/admin unnecessarily

## Automation

## Snapshot Script
```bash
#!/bin/bash

for vm in 102 103 104
do
  qm snapshot $vm auto-snap-$(date +%F)
done
```

## Maintenance Tasks

## Linux
```bash
sudo apt update && sudo apt upgrade -y
sudo apt clean
```

## Windows
Apply updates
Review event logs

## Key Principles
Validate before and after changes
Use snapshots before major operations
Centralize identity via Active Directory
Enforce least privilege
Follow structured troubleshooting
Maintain consistent naming and configuration

## Summary

This runbook provides a structured approach to operating the Northwind Enterprise Lab.

Phase 1 established:

Virtualization and networking foundation

Phase 2 extends operations with:

Active Directory management
Group Policy enforcement
File services administration

It ensures:

Consistent operations
Faster troubleshooting
Reliable recovery
Alignment with real-world infrastructure practices
