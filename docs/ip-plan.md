# IP Plan — Northwind Enterprise Lab

## Overview

This document defines the IP addressing scheme used in the Northwind Enterprise Lab.

The design follows standard enterprise practices:

- Private IP addressing  
- Logical network segmentation  
- Predictable and scalable allocation  
- Centralized gateway and DHCP via pfSense  

---

## Addressing Strategy

The lab uses RFC1918 private address space:

- 10.10.0.0/16 reserved for internal use  
- Subnets segmented by function (Server, Client, DMZ)  

Each subnet is /24 to provide:
- Simplicity  
- Room for growth  
- Easy troubleshooting  

---

## Network Subnets

| Network | Subnet | CIDR | Purpose |
|--------|--------|------|--------|
| Server | 10.10.20.0 | /24 | Internal servers |
| Client | 10.10.30.0 | /24 | User devices |
| DMZ | 10.10.40.0 | /24 | Public-facing services |
| WAN | 192.168.x.x | /24 | External/home network |

---

## Gateway Allocation

pfSense provides gateway IPs for each network:

| Network | Gateway IP |
|--------|-----------|
| Server | 10.10.20.1 |
| Client | 10.10.30.1 |
| DMZ | 10.10.40.1 |

---

## DHCP Allocation

DHCP is managed by pfSense for all internal networks.

### Server Network (10.10.20.0/24)
- Range: 10.10.20.100 – 10.10.20.200  
- Reserved for dynamic server allocation  

### Client Network (10.10.30.0/24)
- Range: 10.10.30.100 – 10.10.30.200  
- Used for user devices  

### DMZ Network (10.10.40.0/24)
- Range: 10.10.40.100 – 10.10.40.200  
- Used for public-facing services  

---

## Static IP Reservations (Recommended)

Certain infrastructure components should use static IPs:

| System | IP Address | Network | Purpose |
|-------|-----------|--------|--------|
| pfSense (Server GW) | 10.10.20.1 | Server | Gateway |
| pfSense (Client GW) | 10.10.30.1 | Client | Gateway |
| pfSense (DMZ GW) | 10.10.40.1 | DMZ | Gateway |
| NW-LX01 | 10.10.20.x | Server | Linux server |
| NW-LX02 | 10.10.20.x | Server | Cloned server |
| NW-WKS01 | 10.10.30.x | Client | Windows client |

Note: Static IPs can be assigned via:
- DHCP reservation (preferred)  
- Manual configuration inside the OS  

---

## DNS Configuration

All internal systems use pfSense as DNS server.

Example:
nameserver 10.10.20.1


Responsibilities:
- Resolve external domains  
- Provide local name resolution (future)  
- Cache DNS queries  

---

## NAT Addressing

Internal networks use private IPs:
10.10.x.x → translated to → 192.168.x.x (WAN)


This is handled by pfSense using outbound NAT.

---

## Address Allocation Logic

Each subnet follows a structured pattern:

| Range | Usage |
|------|------|
| .1 | Gateway (pfSense) |
| .2 – .50 | Reserved (infrastructure/static) |
| .100 – .200 | DHCP pool |
| .201 – .254 | Future expansion |

---

## Naming and Addressing Convention

- Network = Function-based  
  - 20 → Server  
  - 30 → Client  
  - 40 → DMZ  

- VM naming aligned with function:
  - NW-LX01 → Linux server  
  - NW-WKS01 → Workstation  

---

## Scalability Considerations

- Additional subnets can be added:
  - 10.10.50.0/24 (e.g., management network)  
- VLANs can be introduced without changing IP structure  
- DHCP ranges can be expanded or segmented  

---

## Validation Performed

- DHCP assignment verified across all networks  
- Gateway reachability confirmed  
- Inter-network communication tested  
- Internet access validated via NAT  
- DNS resolution confirmed  

---

## Troubleshooting Considerations

Common issues and checks:

- No IP assigned → check DHCP  
- No internet → check gateway/NAT  
- DNS failure → check resolver settings  
- Duplicate IP → check static assignments  
- Clone issues → check MAC address binding  

---

## Design Principles

- Use private IP space internally  
- Separate networks by function  
- Centralize routing through firewall  
- Maintain predictable IP structure  
- Enable easy troubleshooting and expansion  