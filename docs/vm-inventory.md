# VM Inventory

## Purpose
This file tracks the virtual machines planned and/or created as part of the Northwind lab.

| VM Name | Role | OS | Status | Notes |
|---|---|---|---|---|
| NW-DC01 | Domain Controller | Windows Server | Planned | AD DS / DNS |
| NW-FS01 | File Server | Windows Server | Planned | Shares / permissions |
| NW-WKS01 | Client Workstation | Windows 10/11 | Planned | Domain join / GPO testing |
| NW-LX01 | Linux Server | Ubuntu Server | Built / Planned | SSH / NGINX / scripting |
| NW-MGMT01 | Management Box | Windows or Ubuntu | Planned | Admin tools / Git / Terraform |
| NW-K8S01 | Kubernetes Node | Ubuntu Server | Planned | Docker / k3s / Minikube |

## Phase 1 Notes
Phase 1 focused mainly on:
- hypervisor readiness
- base VM creation
- clone behavior
- network functionality
- preparing the platform for later service deployment