# Identity and Access Design

## Overview
This document outlines the design and implementation of identity and access management within the Northwind Enterprise Lab environment.

The objective is to simulate a real-world enterprise Active Directory (AD) environment where users, devices, and resources are centrally managed, and access is controlled using role-based principles.

---

## Domain Configuration

- Domain Name: `northwind.local`
- Domain Controller: `NW-DC01`
- Services Installed:
  - Active Directory Domain Services (AD DS)
  - DNS (AD-integrated)

The Domain Controller serves as the central authority for:
- Authentication
- Authorisation
- Directory services
- DNS resolution for internal resources

---

## Organisational Unit (OU) Structure

A structured OU hierarchy was created to logically organise users, computers, and administrative boundaries.

### Top-Level OUs
- Northwind Users
- Northwind Computers
- Northwind Groups
- Service Accounts

### User OUs
- IT
- HR
- Finance
- Engineering
- Support

### Computer OUs
- Workstations
- Servers

### Design Rationale
- Separation of users, computers, and groups improves manageability
- Enables targeted Group Policy application
- Reflects departmental structure found in enterprise environments

---

## User Accounts

Test user accounts were created to represent different departments:

- john.it
- sarah.hr
- mike.finance
- emma.support
- david.eng

### Implementation Approach
- Users were created using PowerShell scripts
- Naming convention: `firstname.department`
- Accounts configured with:
  - Secure password
  - Enabled status
  - Forced password change at first logon

---

## Security Groups

Security groups were created to support role-based access control.

### Groups
- IT_Admins
- HR_Team
- Finance_Team
- Engineering_Team
- Support_Team

### Group Scope and Type
- Group Scope: Global
- Group Type: Security

### Design Rationale
- Global groups are suitable for grouping users within a domain
- Security groups are used to assign permissions to resources

---

## Role-Based Access Control (RBAC)

Access to resources is not assigned directly to users.

Instead, the following model is used:
Users → Groups → Permissions


### Implementation
- Users are assigned to departmental groups
- Groups are granted access to resources (e.g., file shares, RDP rights)
- No direct user-to-resource permissions are used

### Benefits
- Simplifies administration
- Reduces errors
- Scales effectively in larger environments
- Aligns with enterprise best practices

---

## Domain Join Process

Workstation: `NW-WKS01`

### Configuration Requirements
- DNS must point to Domain Controller (`10.10.20.102`)
- Network connectivity between client and server subnets must be functional

### Steps Performed
1. Configure client DNS to use Domain Controller
2. Verify connectivity using ping and nslookup
3. Join domain `northwind.local`
4. Reboot workstation
5. Log in using domain credentials

### Validation
- Successful login using domain user account
- Domain profile created on workstation
- Group Policy applied

---

## Authentication and Authorisation Flow

### Authentication
- Users authenticate against the Domain Controller
- Credentials are validated via Active Directory

### Authorisation
- Access to resources is determined by group membership
- Permissions are enforced using:
  - NTFS permissions
  - Share permissions
  - Group Policy

---

## Naming Conventions

Consistent naming conventions were used throughout the environment:

### Users
firstname.department

### Groups
Department_Team
IT_Admins


### Servers
NW-DC01
NW-FS01
NW-WKS01


### Benefits
- Improves readability
- Simplifies administration
- Aligns with enterprise standards

---

## Security Considerations

- Least privilege principle applied
- Administrative access restricted to IT_Admins group
- Standard users limited via Group Policy
- Password and lockout policies enforced at domain level

---

## Summary

The identity and access design provides a structured and scalable approach to managing users and resources.

Key outcomes:
- Centralised authentication using Active Directory
- Logical OU structure enabling targeted policy application
- Role-based access control using security groups
- Successful integration of domain-joined client

This implementation reflects real-world enterprise practices and provides a strong foundation for further expansion in later phases.