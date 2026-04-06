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

## Flow:
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


## Lesson

Recursive DNS requires proper internet routing
In NAT or home lab environments, forwarding mode is more reliable
Always consider upstream network topology


## Additional Insight

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

## Issue 12: Domain DNS Lookup Failed From Client

### Symptoms
- `nslookup northwind.local` fails on the Windows client  
- Domain join cannot proceed  
- Client can obtain an IP address, but cannot locate the domain  

### Root Cause
- The client was using pfSense as its DNS server  
- pfSense did not host the AD-integrated DNS zone for `northwind.local`  

### Resolution

#### Check client DNS settings
```powershell
ipconfig /all
```

Initially, the client received:

```text
DNS Server: 10.10.30.1
```

This was corrected by updating the pfSense DHCP scope for the client network so that DHCP handed out the Domain Controller as DNS.

Correct design:

```text
DNS Server: 10.10.20.102
```

#### Renew lease on client
```powershell
ipconfig /release
ipconfig /renew
```

#### Retest
```powershell
nslookup northwind.local
```

### Lesson
- In Active Directory environments, clients must use the Domain Controller as DNS  
- DHCP should distribute the correct DNS settings automatically  

---

## Issue 13: DNS Timeout Even After Client Pointed to DC

### Symptoms
- Client DNS server changed to the Domain Controller  
- `nslookup northwind.local` still times out  
- Client cannot resolve the domain  

### Root Cause
- DNS was no longer the logical design issue  
- The real issue was network connectivity between subnets and incorrect server-side configuration  

### Resolution

#### Validate connectivity first
```powershell
ping 10.10.20.102
```

Once connectivity troubleshooting began, a server-side IP configuration issue was discovered and corrected.

### Lesson
- If a client points to the correct DNS server but still times out, test network reachability before assuming a DNS service failure  
- DNS problems are often routing or host configuration problems in disguise  

---

## Issue 14: Wrong Subnet Mask on Domain Controller

### Symptoms
- Client could not reliably reach the Domain Controller  
- DNS queries timed out  
- Cross-subnet communication was inconsistent or failed  

### Root Cause
The Domain Controller NIC had an incorrect subnet mask configured:

```text
225.0.0.0
```

Correct mask should have been:

```text
255.255.255.0
```

### Resolution

#### Update the NIC configuration on `NW-DC01`
```text
IP Address:      10.10.20.102
Subnet Mask:     255.255.255.0
Default Gateway: 10.10.20.1
Preferred DNS:   10.10.20.102
```

#### Validate
```powershell
ipconfig
ping 10.10.30.100
```

#### Retest from client
```powershell
ping 10.10.20.102
nslookup northwind.local
```

### Lesson
- A single incorrect subnet mask can break routing, DNS, and domain operations  
- Always validate basic IP configuration before deeper troubleshooting  

---

## Issue 15: Client Could Ping DC, but DC Could Not Ping Client

### Symptoms
- Client could ping the Domain Controller successfully  
- Domain DNS resolution worked from the client  
- Domain join succeeded  
- Ping from DC to client timed out  

### Root Cause
- This was not a routing failure  
- The Windows client was most likely blocking inbound ICMP echo requests via host firewall  

### Resolution
Since client → DC communication and domain operations worked, the issue was classified as host firewall behavior, not network failure.

#### Optional validation on the Windows client
```powershell
Enable-NetFirewallRule -DisplayGroup "File and Printer Sharing"
```

### Lesson
- Asymmetric ping does not always mean broken routing  
- Host firewalls can block inbound ICMP while allowing normal business traffic  

---

## Issue 16: Domain Join Dependency on DNS

### Symptoms
- Domain join failed until DNS was corrected  
- Client had valid IP and gateway but could not join `northwind.local`  

### Root Cause
- Active Directory depends on DNS service discovery  
- The client must resolve AD records hosted on the Domain Controller  

### Resolution
After DHCP was updated to hand out the DC as DNS, and DC connectivity was restored, domain join completed successfully.

#### Validation steps
```powershell
nslookup northwind.local
ping 10.10.20.102
```

Then join domain:

```text
System Properties → Change settings → Domain → northwind.local
```

### Lesson
- Domain join is a DNS-dependent process  
- In AD environments, correct DNS is not optional  

---

## Issue 17: UNC Path vs Folder Path Confusion for File Shares

### Symptoms
Uncertainty over whether drive mapping should use:

```text
\\NW-FS01\Finance
```

or

```text
\\NW-FS01\Shares\Finance
```

### Root Cause
Confusion between:
- the underlying folder path on disk  
- the published SMB share name  

### Resolution
Use the share name, not the physical folder path.

#### Example
Folder path on server:

```text
C:\Shares\Finance
```

Share name:

```text
Finance
```

Correct UNC path:

```text
\\NW-FS01\Finance
```

#### Validation
```powershell
Get-SmbShare
```

### Lesson
- Users and GPOs access SMB shares through UNC paths based on the share name  
- The underlying disk path is not used directly by clients  

---

## Issue 18: Drive Mapping and File Access Validation

### Symptoms
Need to confirm mapped drives and file access are functioning correctly for the right users.

### Root Cause
File access depends on multiple layers:
- group membership  
- share permissions  
- NTFS permissions  
- successful GPO application  

### Resolution

Validate in this order:

1. Confirm user is in correct AD security group  
2. Confirm GPO applied:
   ```powershell
   gpresult /r
   ```
3. Confirm mapped drive appears after logon or after:
   ```powershell
   gpupdate /force
   ```
4. Test manual access:
   ```text
   \\NW-FS01\Finance
   ```

### Lesson
- File access issues are rarely caused by one setting alone  
- Validate identity, policy, and permissions together  

---

## General Troubleshooting Commands

### Network

#### Linux
```bash
ip a
ip route
ping 8.8.8.8
ping google.com
```

#### Windows
```powershell
ipconfig
ipconfig /all
ping 10.10.20.102
nslookup northwind.local
nslookup google.com
tracert 10.10.20.102
```

### Active Directory / GPO
```powershell
Get-ADUser -Filter *
Get-ADGroup -Filter *
gpupdate /force
gpresult /r
gpresult /h C:\temp\gpo-report.html
```

### Services

#### Linux
```bash
systemctl status <service>
systemctl restart <service>
```

#### Windows
```powershell
Get-Service dns
Get-Service netlogon
```

### Disk
```bash
df -h
lsblk
```

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
- Separate network, DNS, service, and identity issues  
- Always verify after changes  

---

## Summary

This troubleshooting guide reflects real issues encountered and resolved during the lab setup.

### Phase 1 demonstrated troubleshooting across:
- Linux networking  
- pfSense NAT / DNS behavior  
- cloning and restore operations  

### Phase 2 extends this with:
- Active Directory DNS dependency  
- domain join troubleshooting  
- subnet misconfiguration diagnosis  
- file access and GPO validation  

Together, these scenarios demonstrate structured problem-solving and practical infrastructure troubleshooting aligned with real-world operational support.