# Group Policy Design and Implementation

## Overview
This document outlines the design and implementation of Group Policy within the Northwind Enterprise Lab environment.

The objective is to enforce security, standardisation, and role-based controls across domain-joined systems using a structured and modular Group Policy approach.

---

## Design Principles

### One Purpose per GPO
Each Group Policy Object (GPO) is designed to serve a single purpose.

Benefits:
- Easier troubleshooting
- Clear scope and intent
- Reduced risk of unintended changes

---

### Scope-Based Targeting
GPOs are linked to specific Organizational Units (OUs) based on:
- User role
- Device type

This ensures policies are applied only where required.

---

### Role-Based Control
Policies are aligned with organisational roles:
- IT administrators
- Department users (HR, Finance, etc.)

---

## Implemented GPOs

### 1. NW-Security-Baseline

**Scope:** Domain (`northwind.local`)  
**Type:** Computer Configuration

#### Settings Applied

**Password Policy**
Computer Configuration
→ Policies
→ Windows Settings
→ Security Settings
→ Account Policies
→ Password Policy


- Minimum password length: 8+
- Password complexity: Enabled
- Maximum password age: Defined

**Account Lockout Policy**
Account Lockout Policy


- Lockout threshold: 5 attempts
- Lockout duration: 15 minutes

#### Purpose
- Enforce secure authentication standards
- Protect against brute-force attacks

---

### 2. NW-HR-Restrictions

**Scope:** OU = HR  
**Type:** User Configuration

#### Settings Applied

**Disable Control Panel**

User Configuration
→ Administrative Templates
→ Control Panel
→ Prohibit access to Control Panel and PC settings


#### Purpose
- Restrict system configuration changes
- Apply tighter control to non-technical users

---

### 3. NW-RDP-Restrictions

**Scope:** Workstations / Servers (as applicable)  
**Type:** Computer Configuration

#### Settings Applied

**Allow log on through Remote Desktop Services**

Computer Configuration
→ Windows Settings
→ Security Settings
→ Local Policies
→ User Rights Assignment


- Allowed group: `IT_Admins`

#### Purpose
- Restrict remote administrative access
- Ensure only authorised personnel can access systems via RDP

---

### 4. NW-App-Control

**Scope:** Workstations  
**Type:** User Configuration

#### Settings Applied

**Restrict Application Execution**

User Configuration
→ Administrative Templates
→ System
→ Don't run specified Windows applications


Examples:
- `setup.exe`
- `msiexec.exe`

#### Purpose
- Prevent unauthorised software installation
- Enforce least privilege for standard users

---

### 5. NW-Drive-Mapping

**Scope:** Users OU  
**Type:** User Configuration (Preferences)

#### Settings Applied

**Drive Maps**

User Configuration
→ Preferences
→ Windows Settings
→ Drive Maps


#### Configuration

| Drive Letter | Path                | Target Group     |
|-------------|---------------------|------------------|
| F:          | \\NW-FS01\Finance   | Finance_Team     |
| H:          | \\NW-FS01\HR        | HR_Team          |

#### Targeting Method
- Item-level targeting based on security group membership

#### Purpose
- Provide seamless access to departmental resources
- Ensure users only see relevant drives

---

## GPO Linking Strategy

| GPO Name               | Linked To                  |
|------------------------|----------------------------|
| NW-Security-Baseline   | Domain                     |
| NW-HR-Restrictions     | HR OU                      |
| NW-RDP-Restrictions    | Northwind Computers OU     |
| NW-App-Control         | Northwind Computers OU     |
| NW-Drive-Mapping       | Northwind Users OU         |

---

## Policy Processing

### Update Command
gpupdate /force

### Verification
gpresult /r

Or:
gpresult /h C:\temp\gpo-report.html


---

## Validation

The following were verified:

- Password and lockout policies enforced
- HR users unable to access Control Panel
- Only IT_Admins allowed RDP access
- Standard users restricted from installing applications
- Network drives mapped based on group membership

---

## Troubleshooting Considerations

### GPO Not Applying
- Check OU placement of user/computer
- Verify GPO is linked correctly
- Confirm security filtering (if used)
- Run:
gpresult /r


---

### Drive Not Mapping
- Confirm user is in correct security group
- Verify UNC path accessibility
- Check item-level targeting conditions

---

### Policy Conflicts
- Review GPO link order and inheritance
- Avoid overlapping settings across multiple GPOs

---

## Summary

The Group Policy implementation provides a structured and scalable method of enforcing configuration and security across the environment.

Key outcomes:
- Centralised policy management
- Role-based restrictions
- Automated resource access
- Consistent system configuration

This approach aligns with enterprise best practices and supports efficient administration at scale.