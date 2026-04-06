# DNS and DHCP Integration

## Overview
This document describes the integration of DNS and DHCP within the Northwind Enterprise Lab environment.

The objective is to ensure that all domain-joined clients can correctly locate Active Directory services while maintaining a segmented network architecture using pfSense.

---

## Environment Components

### Network Segmentation
- Management Network: `10.10.10.0/24`
- Server Network: `10.10.20.0/24`
- Client Network: `10.10.30.0/24`
- Gateway/Router: pfSense

### Key Systems
- Domain Controller (DNS): `NW-DC01` → `10.10.20.102`
- DHCP Server: pfSense (Client network)
- Client Workstation: `NW-WKS01`

---

## Initial Configuration (Problem State)

### DHCP Configuration
- DHCP was managed by pfSense
- Clients received:
  - IP address
  - Gateway
  - DNS = pfSense

### Result
Clients attempted to resolve internal domain queries using pfSense.

Example:
# DNS and DHCP Integration

## Overview
This document describes the integration of DNS and DHCP within the Northwind Enterprise Lab environment.

The objective is to ensure that all domain-joined clients can correctly locate Active Directory services while maintaining a segmented network architecture using pfSense.

---

## Environment Components

### Network Segmentation
- Management Network: `10.10.10.0/24`
- Server Network: `10.10.20.0/24`
- Client Network: `10.10.30.0/24`
- Gateway/Router: pfSense

### Key Systems
- Domain Controller (DNS): `NW-DC01` → `10.10.20.102`
- DHCP Server: pfSense (Client network)
- Client Workstation: `NW-WKS01`

---

## Initial Configuration (Problem State)

### DHCP Configuration
- DHCP was managed by pfSense
- Clients received:
  - IP address
  - Gateway
  - DNS = pfSense

### Result
Clients attempted to resolve internal domain queries using pfSense.

Example:
nslookup northwind.local


Outcome:
- DNS query failed
- Domain Controller could not be discovered
- Domain join unsuccessful

---

## Root Cause

Active Directory relies heavily on DNS for service discovery.

Specifically, clients must resolve records such as:
_ldap._tcp.dc._msdcs.northwind.local


These records exist only in:
- Active Directory-integrated DNS (on the Domain Controller)

pfSense DNS does not host or replicate these records.

Therefore:
- Clients using pfSense for DNS cannot locate the Domain Controller
- Authentication and domain operations fail

---

## Solution Implemented

### DHCP Adjustment (pfSense)

The DHCP configuration on the Client network was updated to assign the Domain Controller as the DNS server.

#### Updated DHCP Option
DNS Server: 10.10.20.102


### Resulting Client Configuration
Clients now receive:
- IP address (from pfSense)
- Default Gateway (pfSense)
- DNS Server (Domain Controller)

---

## Final DNS Flow
Client → Domain Controller (DNS) → pfSense → Internet


### Flow Explanation

1. Client sends DNS query to Domain Controller
2. If the query is internal (e.g., `northwind.local`):
   - Domain Controller resolves directly
3. If the query is external (e.g., `google.com`):
   - Domain Controller forwards request to upstream DNS (pfSense or public resolver)

---

## DNS Forwarding Configuration

To support external name resolution:

### On Domain Controller (DNS Manager)
- Forwarders configured to:
  - pfSense IP address (recommended), or
  - Public DNS (e.g., 1.1.1.1, 8.8.8.8)

This ensures:
- Internal and external resolution both function correctly

---

## Validation Steps

### Verify Client DNS Configuration
ipconfig /all


Expected:
DNS Servers . . . . . . . . . . : 10.10.20.102


---

### Test Internal Resolution
nslookup northwind.local


Expected:
- Resolves to Domain Controller IP

---

### Test External Resolution
nslookup google.com


Expected:
- Successful resolution via DNS forwarder

---

### Connectivity Testing
ping 10.10.20.102


Expected:
- Successful communication between client and server networks

---

## Troubleshooting Summary

### Issue 1: DNS Resolution Failure
- Cause: Client using pfSense as DNS
- Fix: DHCP updated to assign Domain Controller as DNS

---

### Issue 2: Connectivity Failure Between Subnets
- Cause: Incorrect subnet configuration and routing assumptions
- Fix:
  - Correct subnet mask
  - Validate pfSense routing and firewall rules

---

### Issue 3: DNS Timeout After Fix
- Cause: Network communication issue between client and Domain Controller
- Fix:
  - Corrected network configuration
  - Verified gateway and subnet alignment

---

## Design Considerations

### Why DHCP Remains on pfSense
- Centralised network control
- Consistent IP addressing across subnets
- Aligns with real-world firewall/router responsibilities

### Why DNS Is Handled by Domain Controller
- Required for Active Directory functionality
- Enables service discovery
- Supports authentication and policy application

---

## Best Practice Summary

- Domain-joined clients must use the Domain Controller as their primary DNS server
- DHCP should distribute DNS settings automatically
- Domain Controller should forward external queries upstream
- Avoid using public DNS directly on domain clients

---

## Summary

The integration of DNS and DHCP is critical for a functional Active Directory environment.

By configuring pfSense DHCP to assign the Domain Controller as the DNS server, the lab achieved:

- Reliable domain resolution
- Successful domain join
- Proper Group Policy application
- Seamless internal and external name resolution

This configuration reflects standard enterprise network design practices.