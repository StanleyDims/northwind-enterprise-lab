# File Services Design and Implementation

## Overview
This document describes the design and implementation of file services within the Northwind Enterprise Lab environment.

The objective is to provide centrally managed file storage with controlled access based on user roles, using industry-standard practices such as Role-Based Access Control (RBAC) and proper permission layering.

---

## File Server Configuration

- Server Name: `NW-FS01`
- Role: File Server
- Storage Location: `C:\Shares`

The file server is domain-joined and integrated with Active Directory for authentication and access control.

---

## Folder Structure

The following directory structure was created on the file server:

```text
C:\Shares
├── Finance
├── HR
├── Shared
├── Support
└── IT-Admin
```

## Share Configuration

Each departmental folder is shared individually.

## Share Names and Paths

| Share Name | Path               | UNC Path           |
| ---------- | ------------------ | -----------------  |
| Finance    | C:\Shares\Finance  | \\NW-FS01\Finance  |
| HR         | C:\Shares\HR       | \\NW-FS01\HR       |
| Shared     | C:\Shares\Shared   | \\NW-FS01\Shared   |
| IT-Admin   | C:\Shares\IT-Admin | \\NW-FS01\IT-Admin |
| Suuport    | C:\Shares\Support  | \\NW-FS01\Support  |

---

## Design Principle

Users access resources via UNC paths, not local file system paths.
Correct access format:
```text
\\NW-FS01\Finance
```

Incorrect format:
```text
C:\Shares\Finance
```

---

## Permission Model

A layered permission model was implemented using:

- Share Permissions
- NTFS Permissions

---

### Share Permissions

Configured to be permissive, allowing access control to be handled primarily at the NTFS level.

Typical configuration:
- Authenticated Users: Read (or Change)
- Administrators: Full Control

---

### NTFS Permissions

Permissions are assigned using security groups.

#### Example: Finance Folder

| Group           | Permission |
|-----------------|------------|
| Finance_Team    | Modify     |
| IT_Admins       | Full Control |
| Others          | No access  |

---

## Role-Based Access Control (RBAC)

The following model is used:

```text
Users → Groups → Permissions
```

### Implementation

- Users are members of departmental groups
- Groups are granted access to folders
- No direct user-to-folder permissions are used

---

## Drive Mapping via Group Policy

Network drives are mapped using Group Policy Preferences.

### Configuration

| Drive Letter | UNC Path             | Target Group   |
|--------------|----------------------|----------------|
| F:           | \\NW-FS01\Finance    | Finance_Team   |
| H:           | \\NW-FS01\HR         | HR_Team        |

### Targeting Method
- Item-level targeting based on group membership

### Result
- Users automatically receive access to relevant drives upon login
- Drives are hidden from users without appropriate group membership

---

## Access Validation

The following checks were performed:

- Finance users can access `\\NW-FS01\Finance`
- HR users can access `\\NW-FS01\HR`
- Users cannot access folders outside their assigned group
- Drives are mapped correctly via GPO

---

## Troubleshooting Scenarios

### Issue: User Cannot Access Share

#### Possible Causes
- User not in correct security group
- Incorrect NTFS permissions
- Share permissions misconfigured

#### Resolution Steps
1. Verify group membership:
whoami /groups

2. Check NTFS permissions on folder

3. Confirm share permissions

4. Test access manually using UNC path

---

### Issue: Drive Not Mapping

#### Possible Causes
- GPO not applied
- Incorrect targeting conditions
- Network connectivity issue

#### Resolution Steps
1. Run:
gpudate /force


2. Verify applied GPOs:
gpresult /r


3. Check UNC path accessibility

---

### Issue: Access Denied

#### Possible Causes
- NTFS permissions overriding share permissions
- Missing group membership

#### Resolution Steps
- Review effective permissions
- Confirm group assignment
- Re-evaluate inheritance settings

---

## Design Considerations

### Why Individual Shares
- Simplifies permission management
- Aligns with departmental access control
- Reduces complexity compared to nested share structures

---

### Why Use Groups Instead of Users
- Easier to manage access changes
- Scales with organisational growth
- Reduces administrative overhead

---

### NTFS vs Share Permissions

- Share permissions control access over the network
- NTFS permissions control access at the file system level
- Effective access is the most restrictive combination of both

---

## Summary

The file services implementation provides a structured and secure method of managing shared resources.

Key outcomes:
- Centralised file storage
- Group-based access control
- Automated drive mapping via GPO
- Clear separation of permissions

This design reflects real-world enterprise file server configurations and supports scalable access management.