# Security Model — Northwind Enterprise Lab

## Overview

This document defines the security model implemented in the Northwind Enterprise Lab.

The design follows layered security principles, including:

- Network segmentation  
- Firewall enforcement  
- Identity and access control  
- Least privilege  
- Group Policy enforcement  
- Controlled administrative access  

The goal is to simulate a realistic enterprise security posture within a lab environment.

---

## Security Objectives

The security model aims to:

- Isolate network segments  
- Control traffic between zones  
- Protect internal resources  
- Centralize authentication and authorization  
- Enforce least privilege access  
- Reduce attack surface  

---

## Security Layers

### 1. Network Segmentation (Phase 1)

The environment is divided into isolated networks:

| Network | Subnet | Trust Level |
|--------|--------|------------|
| WAN | 192.168.x.x | Untrusted |
| DMZ | 10.10.40.0/24 | Low trust |
| Client | 10.10.30.0/24 | Medium trust |
| Server | 10.10.20.0/24 | High trust |

Purpose:
- Limit lateral movement  
- Contain potential compromises  
- Separate user, server, and public systems  

---

### 2. Firewall (pfSense)

pfSense enforces all traffic rules between networks.

#### Default Behavior
- Deny inbound traffic from WAN  
- Allow outbound traffic from internal networks  

---

### Firewall Rules Summary

#### WAN
- Block all inbound traffic by default  

#### Server Network
- Allow Server → any  

#### Client Network
- Allow Client → any  

#### DMZ
- Allow DMZ → WAN  
- Restrict DMZ → internal networks  

---

### NAT

- Outbound NAT enabled  
- Internal IPs translated to WAN IP  

Purpose:
- Hide internal network structure  
- Enable internet access  

---

### 3. Identity & Access Control (Phase 2) 🔥

Active Directory Domain Services (AD DS) is introduced to centralize identity and access management.

#### Domain
- `northwind.local`

#### Key Components
- Domain Controller (NW-DC01)
- Centralized authentication (Kerberos/NTLM)
- AD-integrated DNS

---

### OU Structure

- Northwind Users  
  - IT  
  - HR  
  - Finance  
  - Engineering  
  - Support  

- Northwind Computers  
  - Workstations  
  - Servers  

- Northwind Groups  

---

### Group-Based Access Control (RBAC)

Access is assigned using security groups:

| Group | Purpose |
|------|--------|
| IT_Admins | Administrative access |
| HR_Team | HR resources |
| Finance_Team | Finance resources |
| Engineering_Team | Engineering access |
| Support_Team | Support access |

---

### Access Model

```text
Users → Groups → Permissions
```

Purpose:

Simplifies access management
Enforces least privilege
Improves scalability

## 4. Group Policy Enforcement 🔥

Group Policy is used to enforce centralized security controls.

## Implemented Policies

## Domain-Level Security
Password complexity enforced
Account lockout policy

## User Restrictions
Control Panel access restricted (HR OU)

## Administrative Control
RDP access limited to IT_Admins
Application execution restrictions for standard users

## Endpoint Configuration
Drive mapping via GPO
Consistent environment configuration

## 5. File Services Security 🔥

File access is controlled via:

SMB shares on NW-FS01
Security group-based permissions

## Example
| Resource      | Access       |
| ------------- | ------------ |
| Finance Share | Finance_Team |
| HR Share      | HR_Team      |

## Permission Layers
Share permissions
NTFS permissions

Purpose:

Enforce least privilege
Prevent unauthorized access

## 6. DNS Security (Updated)
Domain Controller acts as primary DNS for clients
pfSense used as upstream resolver

## DNS Flow
Client → DC DNS → pfSense → Internet

Purpose:

Enable Active Directory service discovery
Maintain internal name resolution integrity

## 7. Host-Level Security
## Proxmox Firewall
Enabled at:
Datacenter level
Node level

Default policy:
Input: DROP
Output: ACCEPT

Management port (8006) explicitly allowed

## 8. Service-Level Security

## Linux Servers
Access via SSH
Sudo used for privilege escalation

## Windows Servers
Managed via Active Directory
Access controlled via group membership
---
## Trust Model

| Zone   | Trust Level  | Access                       |
| ------ | ------------ | ---------------------------- |
| WAN    | Untrusted    | No inbound access            |
| DMZ    | Low trust    | Limited outbound             |
| Client | Medium trust | Controlled via GPO           |
| Server | High trust   | Restricted and authenticated |

## Security Scenarios

## Scenario 1: Compromised Client
Access controlled via AD and GPO
Limited access to server resources
Requires authentication for sensitive systems

## Scenario 2: Unauthorized Access Attempt
Blocked at WAN firewall
Internal access requires domain authentication

## Scenario 3: Unauthorized Resource Access
Denied via group-based permissions
Access only granted through correct security groups

## Security Practices Implemented
Network segmentation
Centralized firewall control
Active Directory-based identity management
Role-based access control (RBAC)
Group Policy enforcement
Least privilege access model
Controlled service exposure

## Limitations
No IDS/IPS configured
No endpoint protection
Limited auditing/logging
No MFA implemented
No TLS/HTTPS enforcement for internal services

## Future Improvements
Implement MFA (Entra ID / Azure AD)
Deploy IDS/IPS (Snort/Suricata)
Enable HTTPS for internal services
Centralized logging (SIEM / Log Analytics)
Endpoint protection (Defender / EDR)
Advanced GPO hardening

## Design Principles
Defense in depth
Least privilege
Segmentation over flat networks
Centralized identity and control
Secure by default

## Summary

The security model evolves from network-based protection (Phase 1) to identity-driven security (Phase 2).

It demonstrates:

Network isolation
Centralized authentication
Role-based access control
Policy-driven endpoint management

This layered approach reflects real-world enterprise security design and prepares the environment for further cloud and hybrid security integration.