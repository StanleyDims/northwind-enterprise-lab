# Phase 1 Build Log

## Objective
Build the virtualization and base network foundation for the Northwind Enterprise Lab using Proxmox VE.

## Activities Completed

### 1. Physical server preparation
- Received and prepared physical server hardware
- Investigated storage/controller presentation
- Validated boot behavior and installation path

### 2. Proxmox installation
- Installed Proxmox VE on the server
- Verified management access
- Confirmed hypervisor availability after boot

### 3. Initial networking setup
- Configured host networking
- Began using Proxmox bridge-based networking
- Validated VM attachment to virtual bridge

### 4. VM creation and testing
- Created initial Ubuntu VM
- Performed OS updates and cleanup
- Converted a base VM into a template
- Created clone(s) for repeatable testing

### 5. Clone/network troubleshooting
- Investigated cloned VM network issue
- Identified that guest connectivity behavior did not initially match expectations
- Corrected network identity issue and validated IP assignment
- Performed connectivity testing

### 6. Snapshot thinking
- Established the need to snapshot before major changes
- Defined template and clone handling as part of the standard workflow

## Challenges Encountered
- Storage/controller visibility during server setup
- boot path confusion during early installation work
- clone networking behavior
- MAC/interface-related guest network inconsistency
- temporary connectivity validation issues

## Outcome
Phase 1 successfully established the hypervisor platform and initial VM workflow, creating a stable base for the next phases of the enterprise lab.