# Storage Design — Northwind Enterprise Lab

## Overview

This document defines the storage architecture used in the Northwind Enterprise Lab.

The design focuses on:

- Separation of storage types  
- Efficient disk usage through thin provisioning  
- Flexibility using LVM inside virtual machines  
- Ability to simulate and resolve real-world storage issues  

---

## Storage Architecture

The lab uses Proxmox storage with two primary storage types:

| Storage | Type | Purpose |
|--------|------|--------|
| local | Directory | ISO images, templates, backups |
| local-lvm | LVM-Thin | VM disks |

---

## Storage Types

### local (Directory Storage)

- Used for:
  - ISO images  
  - Backup files  
  - Templates  

- Characteristics:
  - File-based storage  
  - Accessible via Proxmox GUI  
  - Stores `.iso` and `.vma.zst` files  

---

### local-lvm (LVM-Thin Storage)

- Used for:
  - Virtual machine disks  

- Characteristics:
  - Thin provisioning  
  - Efficient disk allocation  
  - Supports snapshots  
  - Fast performance  

---

## Thin Provisioning

Thin provisioning allows:

- Allocating large virtual disks without consuming full physical space  
- Storage to grow as data is written  

Example:

- VM disk assigned: 64GB  
- Actual usage: only what is written  

---

## Disk Layout (Inside Linux VM)

Linux VMs use:

- LVM (Logical Volume Manager)  
- Separate partitions for:
  - `/boot`  
  - root filesystem (`/`)  

Example structure:
# Storage Design — Northwind Enterprise Lab

## Overview

This document defines the storage architecture used in the Northwind Enterprise Lab.

The design focuses on:

- Separation of storage types  
- Efficient disk usage through thin provisioning  
- Flexibility using LVM inside virtual machines  
- Ability to simulate and resolve real-world storage issues  

---

## Storage Architecture

The lab uses Proxmox storage with two primary storage types:

| Storage | Type | Purpose |
|--------|------|--------|
| local | Directory | ISO images, templates, backups |
| local-lvm | LVM-Thin | VM disks |

---

## Storage Types

### local (Directory Storage)

- Used for:
  - ISO images  
  - Backup files  
  - Templates  

- Characteristics:
  - File-based storage  
  - Accessible via Proxmox GUI  
  - Stores `.iso` and `.vma.zst` files  

---

### local-lvm (LVM-Thin Storage)

- Used for:
  - Virtual machine disks  

- Characteristics:
  - Thin provisioning  
  - Efficient disk allocation  
  - Supports snapshots  
  - Fast performance  

---

## Thin Provisioning

Thin provisioning allows:

- Allocating large virtual disks without consuming full physical space  
- Storage to grow as data is written  

Example:

- VM disk assigned: 64GB  
- Actual usage: only what is written  

---

## Disk Layout (Inside Linux VM)

Linux VMs use:

- LVM (Logical Volume Manager)  
- Separate partitions for:
  - `/boot`  
  - root filesystem (`/`)  

Example structure:
Disk → Partition → LVM → Logical Volume → Filesystem


---

## Storage Operations

### Disk Expansion (Proxmox Level)

1. Navigate to:
   - VM → Hardware  

2. Select:
   - Hard Disk  

3. Click:
   - Resize  

4. Add additional space (e.g., +10GB)

---

### Disk Expansion (Linux Level)

After resizing in Proxmox:

1. Verify disk size:
```bash
lsblk

2. Extend partition:
sudo growpart /dev/sda 3

3. Resize physical volume:
sudo pvresize /dev/sda3

4. sudo pvresize /dev/sda3
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv

5. Resize filesysyem:
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv


## Disk Usage Monitoring

Check usage:
df -h

Check block devices:
lsblk


## Disk Exhaustion Simulation

To simulate low disk space:
fallocate -l 5G bigfile
fallocate -l 10G bigfile2


Verify:
df -h

---

## Disk Cleanup

Remove test files:
rm bigfile bigfile2

---


Real-World Scenario

Problem:

Server disk becomes full
Applications fail or crash

Resolution:

Identify disk usage
Expand disk in hypervisor
Extend LVM and filesystem
Restore service



Storage Validation

The following were tested:

Thin provisioning behavior
Disk expansion at hypervisor level
LVM resizing inside Linux
Disk exhaustion and recovery



Limitations

Single storage backend (no redundancy)
No RAID or distributed storage
No external storage integration


Future Improvements

Add additional storage pools
Implement RAID or ZFS
Integrate shared storage
Use Proxmox Backup Server
Monitor storage utilization
Design Principles
Separate storage types by function
Use thin provisioning for efficiency
Enable dynamic disk expansion
Use LVM for flexibility inside VMs
Simulate real-world storage failures

---


Summary

The storage design provides:

Efficient disk utilization
Flexibility through LVM
Ability to scale storage dynamically
Practical experience with real-world storage issues

This aligns with enterprise practices and prepares for more advanced storage solutions in future phases.
