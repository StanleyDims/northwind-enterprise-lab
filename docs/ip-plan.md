# IP Plan

## Objective
This document defines the planned IP addressing model for the Northwind lab.

## Planned Addressing
| Segment | Subnet | Notes |
|---|---|---|
| Management | 10.10.10.0/24 | Proxmox host and admin access |
| Servers | 10.10.20.0/24 | Domain controller, file server, Linux servers |
| Clients | 10.10.30.0/24 | Workstations and user simulation |
| DMZ / App | 10.10.40.0/24 | Test app exposure |

## Example Reserved Addresses
| Device | IP |
|---|---|
| Proxmox Host | 10.10.10.10 |
| NW-DC01 | 10.10.20.10 |
| NW-FS01 | 10.10.20.20 |
| NW-LX01 | 10.10.20.30 |
| NW-WKS01 | 10.10.30.10 |
| NW-MGMT01 | 10.10.10.20 |
| NW-K8S01 | 10.10.40.10 |

## Notes
Actual addressing may evolve during the build, but documenting the intended structure early helps maintain consistency across future phases.