# Troubleshooting Guide — Northwind Enterprise Lab

## Overview

This document captures real troubleshooting scenarios encountered during the setup and operation of the Northwind Enterprise Lab.

It provides:

- Symptoms  
- Root causes  
- Resolution steps  
- Key lessons  

The goal is to demonstrate structured troubleshooting across:

- OS layer  
- Network layer  
- Firewall layer  
- Infrastructure layer  

---

## Troubleshooting Methodology

A layered approach is used:

1. Check IP assignment  
2. Verify interface status  
3. Validate gateway and routing  
4. Test connectivity (IP first, then DNS)  
5. Check firewall and NAT  
6. Verify services  

---

## Issue 1: No IP Address After Cloning

### Symptoms
- VM boots successfully  
- `ip a` shows no IPv4 address  
- Interface may be up but no IP assigned  

### Root Cause
- Netplan configuration tied to old MAC address  
- Cloned VM receives new MAC address  

### Resolution

Check netplan config:
```bash
cat /etc/netplan/*.yaml
``` 

Remove MAC binding:
```yaml
ethernets:
  ens18:
    dhcp4: true
``` 

Apply configuration:
```bash
sudo netplan apply
``` 

---

### Lesson
- Avoid MAC-specific binding in templates  
- Always verify network config after cloning  

---

## Issue 2: Interface Down After Clone

### Symptoms
- Interface exists but shows `state DOWN`  
- No network connectivity  

### Root Cause
- Interface not initialized during boot  

### Resolution

Bring interface up:
```bash
sudo ip link set ens18 up
``` 

Apply network config:
```bash
sudo netplan apply
``` 

---

### Lesson
- Cloned systems may not auto-initialize network interfaces  

---

## Issue 3: Has IP But No Internet Access

### Symptoms
- VM has IP (e.g., 10.10.20.x)  
- Cannot ping external IP (8.8.8.8)  
- Traceroute stops at pfSense  

### Root Cause
- NAT or firewall issue on pfSense  
- Missing or delayed routing state  

### Resolution

Check routing:
```bash
ip route
``` 

Expected:
default via 10.10.20.1


Check pfSense:
- Firewall rules (LAN → any)  
- NAT mode (Automatic outbound NAT)  

Test pfSense connectivity:
- Diagnostics → Ping → 8.8.8.8  

---

### Lesson
- If IP ping fails → not a DNS issue  
- Always validate layer-by-layer  

---

## Issue 4: DNS Resolution Delay

### Symptoms
- First ping to domain (e.g., google.com) is slow  
- Subsequent requests are fast  

### Root Cause
- Initial DNS lookup and caching  

### Resolution
- No action required  
- Confirm DNS is working  

Check:
```bash
cat /etc/resolv.conf
``` 

---

### Lesson
- DNS latency on first request is normal  
- Distinguish delay from failure  

---

## Issue 5: No Console Access After Template Conversion

### Symptoms
- VM cannot be started  
- No console available  

### Root Cause
- VM converted to template  

### Resolution

Templates:
- Cannot be started  
- Used only for cloning  

To revert:
- Convert template back to VM  

---

### Lesson
- Templates are not active machines  
- Always clone from templates  

---

## Issue 6: Network Works After Delay

### Symptoms
- Initial connectivity issues  
- Network starts working after some time  

### Root Cause
- DHCP lease delay  
- ARP table update  
- pfSense state table refresh  

### Resolution
Force refresh:
```bash
sudo netplan apply
sudo systemctl restart systemd-networkd
``` 

---

### Lesson
- Network state may take time to stabilize  
- Not all issues are configuration errors  

---

## Issue 7: Wrong Network Assignment

### Symptoms
- VM can access unintended resources  
- Network segmentation not enforced  

### Root Cause
- VM connected to wrong bridge (e.g., vmbr1 instead of vmbr2)  

### Resolution
- Update VM network adapter in Proxmox  
- Assign correct bridge  

---

### Lesson
- Bridge = network segment  
- Correct placement is critical  

---

## Issue 8: Service Not Accessible

### Symptoms
- NGINX installed but not reachable from client  

### Root Cause
- Service not running  
- Firewall blocking  
- Network misconfiguration  

### Resolution

Check service:
```bash
systemctl status nginx
``` 

Restart:
```bash
sudo systemctl restart nginx
``` 

Test locally:
```bash
curl http://localhost
``` 

Test from client:
- Browser → http://<server-ip>  

---

### Lesson
- Always isolate:
  - Service issue  
  - Network issue  

---

## Issue 9: Disk Full Scenario

### Symptoms
- System slow or failing  
- No space left on device  

### Root Cause
- Disk exhaustion  

### Resolution

Check usage:
```bash
df -h
``` 

Free space or extend disk:
- Resize in Proxmox  
- Extend LVM  

---

### Lesson
- Disk monitoring is critical  
- Storage must be scalable 


## Issue 10: No Internet Access Behind NAT (pfSense DNS Resolver Issue)

### Symptoms

- VM has valid IP (e.g., 10.10.20.x)  
- Cannot ping external IP (8.8.8.8)  
- Cannot resolve domain names (google.com)  
- Package installation fails (apt update, traceroute install)  
- Affects cloned or newly configured VMs  

---

### Root Cause

pfSense was deployed behind another router (double NAT environment).

By default:
- pfSense DNS Resolver runs in **recursive mode**
- Recursive mode requires direct internet access

In a NAT environment:
- Recursive DNS may fail  
- DNS queries do not resolve properly  

---

### Resolution

Enable DNS forwarding in pfSense.

Steps:

1. Navigate to:
   - Services → DNS Resolver  

2. Enable:
   - "Enable Forwarding Mode"  

3. Save and apply changes  

---

### What This Does

- pfSense forwards DNS queries to upstream DNS servers  
- Instead of performing full recursive resolution  

Flow:
VM → pfSense → Upstream DNS (router or public DNS)

---

### Validation

From Linux VM:

```bash
ping 8.8.8.8
ping google.com
``` 

Expected:

IP connectivity works
DNS resolution works


---


Lesson

Recursive DNS requires proper internet routing
In NAT or home lab environments, forwarding mode is more reliable
Always consider upstream network topology


Additional Insight

In enterprise environments:

Recursive DNS is preferred when:
Firewall has direct internet access

Forwarding DNS is preferred when:
Behind NAT
Using upstream DNS infrastructure

---

## VMs with identical Machine ID. e.g MAC Address

## Root Cause
The issue was caused by restoring a VM backup without enabling the "Unique" option in Proxmox.

This resulted in:
- Duplicate machine identity  
- Network conflicts  
- DHCP inconsistencies  

### Resolution

Ensure the "Unique" option is selected during restore or cloning to generate a new system identity.

### Lesson

Not all network issues originate from OS configuration.  
VM-level identity conflicts can cause similar symptoms.

---

## General Troubleshooting Commands

### Network

```bash
ip a
ip route
ping 8.8.8.8
ping google.com
```

---

### Services

```bash
systemctl status <service>
systemctl restart <service>
``` 

---

### Disk

```bash
df -h
lsblk
```

---

### Processes

```bash
top
htop
``` 

---

## Key Principles

- Troubleshoot layer by layer  
- Do not assume root cause  
- Validate each component independently  
- Separate network, DNS, and service issues  
- Always verify after changes  

---

## Summary

This troubleshooting guide reflects real issues encountered and resolved during the lab setup.

It demonstrates:

- Structured problem-solving  
- Understanding of system layers  
- Ability to diagnose and resolve infrastructure issues  

This aligns with real-world operational and support scenarios.