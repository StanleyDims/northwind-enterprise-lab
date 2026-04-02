# Snapshot and Template Strategy — Northwind Enterprise Lab

## Overview

This document defines how snapshots and templates are used in the Northwind Enterprise Lab to:

- Protect systems before changes  
- Enable rapid recovery from failures  
- Standardize server builds  
- Scale infrastructure efficiently  

The strategy separates **short-term rollback (snapshots)** from **long-term reuse (templates)** and **durable recovery (backups)**.

---

## Definitions

### Snapshot
A point-in-time state of a VM used for quick rollback.

### Template
A read-only master image used to create new VMs consistently.

### Backup
A full VM copy stored separately for long-term recovery.

---

## Snapshot Strategy

### Purpose

- Provide fast rollback before changes  
- Support testing and troubleshooting  
- Minimize downtime during failures  

---

### When to Take Snapshots

Snapshots are created before:

- System updates (`apt upgrade`)  
- Application installation or upgrades  
- Configuration changes (network, services)  
- Security changes (firewall rules, access control)  

---

### Naming Convention

Snapshots follow a consistent format:
<vm-name>-<action>-<date>


Examples:
nw-lx01-before-nginx-2026-03-30
nw-lx01-pre-update-2026-03-30
auto-snap-2026-03-30

---

### Creation Methods

#### GUI (Proxmox)
- VM → Snapshots → Take Snapshot  

#### CLI
```bash
qm snapshot <vmid> <snapshot-name>
``` id="snap_cli"

Example:
```bash
qm snapshot 104 before-change
``` id="snap_cli_ex"

---

### Rollback

#### GUI
- VM → Snapshots → Select → Rollback  

#### CLI
```bash
qm rollback <vmid> <snapshot-name>
``` id="snap_rollback"

---

### Limitations

- Stored on same storage as VM  
- Not suitable for long-term retention  
- Consumes additional storage over time  
- Does not protect against host failure  

---

## Template Strategy

### Purpose

- Standardize server deployments  
- Reduce provisioning time  
- Ensure consistency across environments  

---

### Template Preparation

Before converting a VM to a template:

1. Update system:
```bash
sudo apt update && sudo apt upgrade -y
``` id="tpl_update"

2. Clean cache:
```bash
sudo apt clean
``` id="tpl_clean"

3. (Optional) Reset machine ID:
```bash
sudo truncate -s 0 /etc/machine-id
``` id="tpl_machine_id"

4. Ensure:
- SSH installed and enabled  
- Required base packages installed  
- No environment-specific configurations  

---

### Convert to Template

#### GUI
- VM → More → Convert to template  

---

### Clone from Template

#### GUI
- Right-click template → Clone  

Options:
- Full clone (independent disk)  
- Linked clone (shares base disk)  

Recommended:
- Full clone for lab and stability  

---

### Naming Convention

Cloned VMs follow:
nw-lx01 → template
nw-lx02 → clone
nw-lx03 → additional clones



---

### Post-Clone Tasks

After cloning, verify:

1. Network configuration  
   - Ensure correct interface name  
   - Remove MAC address binding if present  

2. IP assignment  
   - Confirm DHCP lease  

3. Hostname update:
```bash
sudo hostnamectl set-hostname <new-name>
``` id="tpl_hostname"

---

### Common Issues

| Issue | Cause | Resolution |
|------|------|-----------|
| No IP after clone | MAC mismatch in netplan | Remove or update MAC binding |
| Interface down | Network not initialized | Bring interface up |
| Duplicate hostname | Template reused | Update hostname |

---

## Backup Integration

Snapshots and templates are complemented by backups:

- Snapshots → short-term rollback  
- Templates → standardization  
- Backups → full recovery  

Backups are:
- Scheduled via Proxmox  
- Stored on separate storage  
- Used for disaster recovery  

---

## Operational Workflow

### Standard Change Process

1. Take snapshot  
2. Apply change  
3. Validate system  

If failure:
- Rollback snapshot  

---

### Deployment Process

1. Prepare template  
2. Clone new VM  
3. Apply environment-specific configuration  
4. Validate service  

---

### Recovery Process

1. Attempt snapshot rollback  
2. If unavailable → restore from backup  
3. Validate service  

---

## Real-World Mapping

### Snapshot Use Case
> A system update breaks a service

Action:
- Rollback snapshot  
- Restore service immediately  

---

### Template Use Case
> Multiple servers required for scaling

Action:
- Clone from template  
- Deploy in minutes  

---

### Combined Use Case
> Environment expansion and testing

Action:
- Clone multiple VMs  
- Snapshot before testing  
- Rollback if needed  

---

## Design Principles

- Separate rollback, reuse, and recovery mechanisms  
- Use snapshots for speed  
- Use templates for consistency  
- Use backups for safety  
- Validate all recovery processes  

---

## Summary

This strategy ensures:

- Fast recovery from failures  
- Consistent and repeatable deployments  
- Efficient scaling of infrastructure  
- Alignment with enterprise operational practices