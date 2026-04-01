# Backup and Recovery Strategy — Northwind Enterprise Lab

## Overview

This document defines the backup and recovery approach used in the Northwind Enterprise Lab.

The strategy is designed to ensure:

- Data protection  
- System recoverability  
- Minimal downtime during failures  
- Validation of disaster recovery processes  

It complements snapshots (short-term rollback) and templates (standardization) by providing **long-term recovery capability**.

---

## Backup Objectives

The backup strategy aims to:

- Protect virtual machines from data loss  
- Enable full system restoration  
- Support recovery from system failure or corruption  
- Validate recoverability through testing  

---

## Backup Types

### Snapshots (Short-term)
- Stored with the VM  
- Used for quick rollback  
- Not suitable for long-term recovery  

### Backups (Long-term)
- Full VM copies  
- Stored separately from running VM  
- Used for disaster recovery  

---

## Backup Configuration

Backups are configured using Proxmox backup jobs.

### Configuration Details

- Scope: Selected VMs (e.g., NW-LX01)  
- Mode: Snapshot mode  
- Compression: zstd  
- Storage: local  
- Schedule: Daily (21:00)  

---

## Backup Storage

Backups are stored in:

- Proxmox storage: `local`  
- Format: `.vma.zst`  

Example:
vzdump-qemu-102-2025_03_31-21_00_02.vma.zst

---

## Backup Process

### Manual Backup

1. Navigate to VM → Backup  
2. Click "Backup now"  
3. Start backup  

---

### Scheduled Backup

- Configured at datacenter level  
- Runs automatically based on defined schedule  
- Ensures consistent protection  

---

## Recovery Process

### Restore to New VM

1. Navigate to:
   - Node → local → Backup  

2. Select backup file  

3. Click:
   - Restore  

4. Specify:
   - New VM ID  
   - New VM name  

5. Start restore  

---

### Post-Restore Validation

After restoring:

- Start VM  
- Verify network connectivity  
- Check services (e.g., NGINX)  

Example:
```bash
systemctl status nginx
``` id="restore_check"

---

## Recovery Scenarios

### Scenario 1: Service Failure

Problem:
- Application not functioning  

Action:
- Restore snapshot or restart service  

---

### Scenario 2: VM Corruption

Problem:
- VM becomes unstable or unusable  

Action:
- Restore VM from backup  
- Validate system state  

---

### Scenario 3: Complete VM Loss

Problem:
- VM deleted or disk failure  

Action:
- Restore from backup to new VM  
- Reassign role and validate  

---

## Recovery Testing

Recovery was validated through:

- Restoring backup to new VM (NW-LX01-RESTORE)  
- Verifying service availability (NGINX)  
- Testing connectivity from client VM  

This confirms that backups are functional and usable.

---

## RTO and RPO (Basic)

### Recovery Time Objective (RTO)
- Estimated time to restore VM: minutes  
- Depends on VM size and storage speed  

### Recovery Point Objective (RPO)
- Based on backup schedule (daily)  
- Maximum data loss: up to 24 hours  

---

## Limitations

- Backups stored on same Proxmox host  
- No offsite or external backup  
- No redundancy across hosts  

---

## Future Improvements

- Offsite backups (cloud or external storage)  
- Proxmox Backup Server integration  
- Incremental backups  
- Automated restore testing  
- High availability setup  

---

## Design Principles

- Separate backup from running system  
- Validate backups through restore testing  
- Automate backup processes  
- Maintain consistent scheduling  
- Ensure recoverability over convenience  

---

## Summary

The backup and recovery strategy ensures that:

- Virtual machines are protected against failure  
- Systems can be restored quickly  
- Recovery processes are tested and reliable  

This approach aligns with real-world enterprise practices and provides a strong foundation for resilient infrastructure.