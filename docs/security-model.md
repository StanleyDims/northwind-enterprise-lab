# Security Model — Northwind Enterprise Lab

## Overview

This document defines the security model implemented in the Northwind Enterprise Lab.

The design follows layered security principles, including:

- Network segmentation  
- Firewall enforcement  
- Access control  
- Least privilege  
- Controlled administrative access  

The goal is to simulate a realistic enterprise security posture within a lab environment.

---

## Security Objectives

The security model aims to:

- Isolate network segments  
- Control traffic between zones  
- Protect internal resources  
- Restrict administrative access  
- Reduce attack surface  

---

## Security Layers

### 1. Network Segmentation

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

#### Server Network (LAN)
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

### 3. Access Control

#### Linux Servers
- Access via SSH  
- Password-based authentication (lab)  
- Use of sudo for administrative tasks  

#### Proxmox
- Non-root user created  
- Role-based access control (RBAC) applied  

#### pfSense
- Access via web interface  
- Restricted to internal network  

---

### 4. Host-Level Security

#### Proxmox Firewall

- Enabled at:
  - Datacenter level  
  - Node level  

- Default policy:
  - Input: DROP  
  - Output: ACCEPT  

- Management port (8006) explicitly allowed  

---

### 5. Service-Level Security

#### NGINX (Linux Server)

- Runs on port 80  
- Accessible only from internal networks  

Future enhancements:
- HTTPS configuration  
- Reverse proxy setup  

---

### 6. DNS Security

- pfSense acts as DNS resolver  
- DNS forwarding enabled (when behind NAT)  

Purpose:
- Ensure reliable name resolution  
- Prevent DNS failures in NAT environments  

---

## Trust Model

| Zone | Trust Level | Access |
|------|------------|--------|
| WAN | Untrusted | No inbound access |
| DMZ | Low trust | Limited outbound |
| Client | Medium trust | Controlled access |
| Server | High trust | Restricted exposure |

---

## Security Scenarios

### Scenario 1: Compromised Client

- Client network isolated from server network  
- Firewall limits access  
- Prevents direct lateral movement  

---

### Scenario 2: Compromised DMZ Host

- DMZ cannot freely access internal servers  
- Damage contained within DMZ  

---

### Scenario 3: Unauthorized Access Attempt

- WAN traffic blocked  
- Only internal access allowed  

---

## Security Practices Implemented

- Network segmentation  
- Centralized firewall control  
- Use of private IP addressing  
- Role-based access in Proxmox  
- Minimal reliance on root account  
- Controlled service exposure  

---

## Limitations

- No IDS/IPS configured  
- No endpoint protection  
- No centralized authentication (yet)  
- No TLS/HTTPS for services  

---

## Future Improvements

- Implement Active Directory (Phase 2)  
- Enforce SSH key-based authentication  
- Deploy reverse proxy in DMZ  
- Add IDS/IPS (Snort/Suricata)  
- Enable HTTPS for services  
- Centralized logging and monitoring  

---

## Design Principles

- Defense in depth  
- Least privilege  
- Segmentation over flat networks  
- Centralized control through firewall  
- Secure by default  

---

## Summary

The security model provides a layered approach to protecting the lab environment.

It demonstrates:

- Network isolation  
- Controlled access  
- Basic hardening practices  

and establishes a strong foundation for more advanced security implementations in future phases.