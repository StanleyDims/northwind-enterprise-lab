# Architecture Overview

## Summary
Phase 1 established the virtualization and base networking foundation for the Northwind Hybrid Lab.

The environment is hosted on a physical server running Proxmox VE. The purpose of this phase was to create a stable platform on which later phases will be built, including Windows Server, file services, Linux administration, Azure integration, and Kubernetes workloads.

## Core Components
- Physical server hardware
- Proxmox VE hypervisor
- Local storage for VM disks
- Virtual networking via Proxmox bridges
- Planned segmented lab networks
- Base VM creation workflow
- Snapshot and rollback capability
-Backups and recovery

## Design Intent
This phase was focused on establishing:
- a reusable virtualization platform
- realistic network separation
- a documented IP addressing scheme
- a repeatable VM build workflow
- recoverability through snapshots and backup thinking

## Planned VM Roles
- NW-DC01 - Domain Controller
- NW-FS01 - File Server
- NW-WKS01 - Windows Client
- NW-LX01 - Linux Server
- NW-MGMT01 - Management Box
- NW-K8S01 - Kubernetes Node