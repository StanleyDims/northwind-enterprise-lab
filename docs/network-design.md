# Network Design

## Objective
The aim of the Phase 1 network design was to create a simple but realistic segmented structure for the Northwind lab environment.

## Planned Networks
| Network | Purpose | Subnet |
|---|---|---|
| Mgmt | Proxmox management / administration | 10.10.10.0/24 |
| Server | Internal server workloads | 10.10.20.0/24 |
| Client | User devices / workstation simulation | 10.10.30.0/24 |
| DMZ/Lab-App | Test application exposure | 10.10.40.0/24 |

## Initial Implementation Notes
In the actual build, networking began with a simpler working configuration to get Proxmox and the first VMs online. More advanced segmentation will be introduced progressively as the lab matures.

## Proxmox Networking Concepts Used
- vmbr bridges
- physical NIC uplink
- guest virtual NIC attachment
- DHCP/static addressing depending on VM role
- MAC address awareness during clone operations

## Important Observation
During cloning/testing, one network issue encountered was that a cloned VM initially failed to obtain expected connectivity until MAC/network alignment issues were corrected. This reinforced the importance of validating:
- interface naming
- DHCP expectations
- guest network config
- cloned NIC identity
- Proxmox bridge attachment

## Future Direction
Phase 2 and beyond will expand this into clearer isolation between:
- management traffic
- server traffic
- client traffic
- application testing traffic