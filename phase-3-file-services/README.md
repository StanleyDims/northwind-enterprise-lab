## Phase 3 — File Services & Access Control

### Overview

This phase focuses on implementing centralized file services and enforcing controlled access using **Active Directory-based Role-Based Access Control (RBAC)**.

The objective is to simulate real-world enterprise file server management, where access to resources is granted based on user roles, with proper separation between departments and administrative access.

---

### Implementation Summary

The file services role is deployed on:

* **Server:** `NW-FS01`
* **Role:** File Server (SMB)

#### Folder Structure

```text
C:\Shares
├── Finance
├── HR
├── Shared
├── Support
└── IT-Admin
```

#### Shared Resources

| Share Name | UNC Path          |
| ---------- | ----------------- |
| Finance    | \NW-FS01\Finance  |
| HR         | \NW-FS01\HR       |
| Shared     | \NW-FS01\Shared   |
| Support    | \NW-FS01\Support  |
| IT-Admin   | \NW-FS01\IT-Admin |

---

### Access Control Model

A layered permission model is implemented:

* **Share Permissions** → Broad access (network-level)
* **NTFS Permissions** → Granular access control (file system-level)

#### RBAC Design

```text
Users → Groups → Permissions
```

* Users are assigned to **departmental security groups**
* Groups are granted access to specific folders
* No direct user-to-folder permissions are used

#### Example

| Folder   | Group        | Access Level |
| -------- | ------------ | ------------ |
| Finance  | Finance_Team | Modify       |
| HR       | HR_Team      | Modify       |
| IT-Admin | IT_Admins    | Full Control |

---

### Drive Mapping via Group Policy

Network drives are mapped automatically using **Group Policy Preferences** with item-level targeting.

| Drive Letter | Share Path       | Target Group |
| ------------ | ---------------- | ------------ |
| F:           | \NW-FS01\Finance | Finance_Team |
| H:           | \NW-FS01\HR      | HR_Team      |

* Drives are assigned based on group membership
* Users only see resources relevant to their role

---

### Validation & Testing

#### Positive Tests

* Finance users can access Finance share
* HR users can access HR share
* Users can read/write within permitted folders
* Drives are mapped automatically on login

#### Negative Tests

* Finance users are denied access to HR share
* HR users are denied access to Finance share
* Non-admin users cannot access IT-Admin share

---

### Troubleshooting Scenarios

Common issues tested:

#### User Cannot Access Share

* Verified group membership (`whoami /groups`)
* Checked NTFS and share permissions
* Confirmed correct login session (logoff/logon)

#### Drive Not Mapping

* Forced policy update:

  ```bash
  gpupdate /force
  ```
* Verified applied GPOs:

  ```bash
  gpresult /r
  ```

#### Access Denied

* Reviewed effective permissions
* Identified missing group membership or restrictive NTFS rules

---

### Key Skills Demonstrated

* Windows File Server administration
* NTFS and Share permission management
* Role-Based Access Control (RBAC)
* Group Policy (GPO) drive mapping
* Access troubleshooting and validation
* Enterprise-style access design

---

### Supporting Documentation

* Detailed design and implementation:
  → `docs/file-services.md`

* Troubleshooting scenarios:
  → `docs/troubleshooting/`

* Screenshots:
  → `screenshots/phase-3/`

---

### Outcome

This phase demonstrates the ability to design, implement, and support secure file services in a domain environment, reflecting real-world enterprise infrastructure practices.
