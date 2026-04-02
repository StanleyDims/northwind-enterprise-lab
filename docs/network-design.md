# Network Design — Northwind Enterprise Lab

## Overview

This document defines the network architecture and segmentation for the Northwind Enterprise Lab.

The network is designed to simulate a real-world enterprise environment with:

- Segmented networks for security and control  
- Centralized routing via a firewall (pfSense)  
- Controlled communication between network zones  
- Internet access via NAT  

---

## Network Architecture

The lab uses a virtualized network implemented in Proxmox using Linux bridges and pfSense.

graph LR

    Internet["Internet"]
    pfSense["FW01 (pfSense)<br/>Firewall / Router"]

    subgraph ClientNet["Client Network (10.10.30.0/24)"]
        WKS01["NW-WKS01<br/>Windows Client"]
    end

    subgraph ServerNet["Server Network (10.10.20.0/24)"]
        LX02["NW-LX02-CLONED<br/>Ubuntu + NGINX"]
        LX03["NW-LX03-RESTORE<br/>Recovered VM"]
    end

    subgraph DMZNet["DMZ Network (10.10.40.0/24)"]
        DMZ1["Future DMZ Services"]
    end

    WKS01 <-->|HTTP / SSH| pfSense
    pfSense <-->|HTTP / SSH| LX02
    pfSense <-->|HTTP / SSH| LX03

    pfSense <-->|Restricted DMZ traffic| DMZ1

    pfSense <-->|Outbound NAT / Replies| Internet

### Core Components

- Proxmox Bridges (virtual switches)
- pfSense (firewall/router)
- Virtual machines connected to segmented networks

---

## Network Segmentation

| Network | Bridge | Subnet | Purpose |
|--------|--------|--------|--------|
| WAN | vmbr0 | 192.168.x.x | External network (home/ISP) |
| Server | vmbr1 | 10.10.20.0/24 | Internal servers |
| Client | vmbr2 | 10.10.30.0/24 | User devices |
| DMZ | vmbr3 | 10.10.40.0/24 | Public-facing services |

---

## Bridge Mapping (Proxmox)

| Bridge | Type | Description |
|--------|------|------------|
| vmbr0 | External | Connected to physical NIC (nic0) |
| vmbr1 | Internal | Server network |
| vmbr2 | Internal | Client network |
| vmbr3 | Internal | DMZ network |

---

## pfSense Interface Mapping

| Interface | Network | Purpose |
|----------|--------|--------|
| WAN | vmbr0 | Internet access |
| LAN | vmbr1 | Server network |
| OPT1 (CLIENT) | vmbr2 | Client network |
| OPT2 (DMZ) | vmbr3 | DMZ network |

---

## IP Addressing Scheme

| Network | Gateway | DHCP Range |
|--------|--------|-----------|
| Server | 10.10.20.1 | 10.10.20.100 – 10.10.20.200 |
| Client | 10.10.30.1 | 10.10.30.100 – 10.10.30.200 |
| DMZ | 10.10.40.1 | 10.10.40.100 – 10.10.40.200 |

---

## DHCP Design

DHCP is provided by pfSense for all internal networks.

Responsibilities:
- Assign IP addresses to VMs  
- Provide default gateway (pfSense interface IP)  
- Provide DNS (pfSense resolver)  

---

## Routing Design

pfSense acts as the central router.

### Internal Routing

- Routes traffic between:
  - Server network  
  - Client network  
  - DMZ  

### Default Gateway

All VMs use:

- Server → 10.10.20.1  
- Client → 10.10.30.1  
- DMZ → 10.10.40.1  

---

## NAT (Network Address Translation)

pfSense performs outbound NAT:


Internal IP (10.10.x.x) → WAN IP (192.168.x.x)


This allows:
- Internet access for all internal networks  
- Private addressing internally  

Mode:
- Automatic outbound NAT  

---

## DNS Design

- pfSense acts as DNS resolver  
- VMs use pfSense as primary DNS  

Example:
nameserver 10.10.20.1


pfSense:
- Resolves external domains  
- Caches DNS responses  

---

## Traffic Flow

### Client to Server

Client VM → pfSense → Server VM


### Client to Internet

Client VM → pfSense (NAT) → Internet


### Server to Internet

Server VM → pfSense (NAT) → Internet


### DMZ Access

DMZ VM → pfSense → Internet


Restricted:

DMZ → Server network (controlled)



---

## Firewall Rules Design

### WAN
- Block inbound traffic by default  

### Server (LAN)
- Allow Server → any  

### Client
- Allow Client → any  

### DMZ
- Allow DMZ → WAN  
- Restrict DMZ → internal networks  

---

## Security Zones

| Zone | Trust Level |
|------|------------|
| WAN | Untrusted |
| DMZ | Low trust |
| Client | Medium trust |
| Server | High trust |

---

## Network Validation

The following tests were performed:

- DHCP assignment verified  
- Gateway reachability (ping pfSense)  
- Internet connectivity (ping 8.8.8.8)  
- DNS resolution (ping google.com)  
- Inter-network communication tested  

---

## Troubleshooting Scenarios Covered

- DHCP not assigning IP  
- Interface down after cloning  
- MAC address mismatch  
- Missing default gateway  
- NAT misconfiguration  
- DNS resolution delays  

---

## Design Principles

- Segmentation improves security  
- Firewall controls all traffic  
- No direct communication without rules  
- Internal networks use private IP space  
- NAT enables internet access  

---

## Future Enhancements

- VLAN implementation  
- Load balancing (NGINX / HAProxy)  
- Reverse proxy in DMZ  
- Zero trust segmentation  
- Integration with Azure networking  