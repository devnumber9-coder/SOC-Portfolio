# Active Directory Workstation Hardening & GPO Baseline

## Overview
This lab demonstrates the deployment of a hardened Windows 11 workstation using **Active Directory**, **Group Policy**, and **least-privilege access controls**.  
The goal was to simulate enterprise workstation security standards and validate policy enforcement.

---

## Lab Environment
- Windows Server (Domain Controller)
- Windows 11 Client (Domain-joined)
- Domain: lab.local
- Virtualization: VirtualBox

---

## Objectives
- Create AD users and workstation OU
- Implement role-based local admin access
- Apply workstation security baseline via GPO
- Enforce audit logging and inactivity lock
- Validate policy application and permissions

---

## Active Directory Configuration
- Created users: `alice`, `bob`
- Created OU: `WorkstationsLab`
- Created security group: `GG_Win11_LocalAdmins`
- Added `alice` to local admin group
- Left `bob` as standard domain user

---

## Group Policy Configuration

### GPO: Workstations Baseline
Linked to `WorkstationsLab` OU

**Security Settings**
- Disable Guest account
- Interactive logon inactivity limit: 600 seconds
- Audit:
  - Logon events (Success/Failure)
  - Account management
  - Group membership changes

**Windows Update**
- Auto-download and notify for install
- Scheduled maintenance enabled

---

### GPO: Local Admin Control
- Restricted Groups used to control local Administrators
- Only `Domain Admins` and `GG_Win11_LocalAdmins` allowed

---

## Verification & Validation
- `gpresult /r` confirms applied GPOs
- `whoami` confirms user context
- `net localgroup administrators` verifies group enforcement
- UAC prompt confirms `bob` is NOT a local admin
- Successful domain join and policy refresh confirmed

---

## Key Skills Demonstrated
- Active Directory user & computer management
- Group Policy design and troubleshooting
- Least privilege enforcement
- Windows security auditing
- Enterprise workstation hardening

---

## Screenshots
See `/screenshots/` for step-by-step evidence of configuration and validation.
