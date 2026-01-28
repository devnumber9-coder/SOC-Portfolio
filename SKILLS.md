# Skills Demonstrated
> Skills demonstrated through hands-on Active Directory, Windows security, and SOC detection labs.

---

## Identity & Access Management (Active Directory)
- Active Directory user, group, and computer object management
- Organizational Unit (OU) design and policy scoping
- Security group creation and role-based access control (RBAC)
- Local vs domain administrator access control
- Authentication vs authorization enforcement
- DNS dependency troubleshooting for Active Directory
- Netlogon service validation
- Kerberos authentication flow analysis (TGT and service tickets)
- Baseline vs abnormal Kerberos behavior identification
- Privileged logon context analysis (Event ID 4672)
- Identity-based threat detection without reliance on failed logons

---

## Windows & Endpoint Security
- Group Policy Object (GPO) creation and management
- Workstation security baseline configuration
- Restricted Groups policy for local administrator control
- Account lockout and inactivity policies
- Windows Update policy configuration
- User Account Control (UAC) behavior validation
- Domain-joined workstation administration
- Windows authentication session lifecycle analysis (logon / logoff)

---

## SIEM & Log Analysis
- Splunk Enterprise search and SPL development
- Windows Security Event Log analysis
- Event ID analysis (4624, 4625, 4768, 4769, 4776)
- Correlation of Kerberos authentication and service ticket events
- Session-based event correlation across authentication lifecycle
- Dashboard creation for security monitoring
- Alert development and tuning

---

## Detection Engineering
- Failed authentication detection (brute force / password spray)
- Kerberos service ticket abuse detection (Event ID 4769)
- Authentication and service ticket correlation logic
- Detection of quiet identity abuse scenarios
- Threshold-based and frequency-based detection logic
- Time-binning and trend analysis
- MITRE ATT&CK mapping:
  - T1110 – Brute Force
  - T1558 – Steal or Forge Kerberos Tickets

---

## Tools & Technologies
- Active Directory Domain Services (AD DS)
- Group Policy Management Console (GPMC)
- Windows Event Viewer
- Splunk Enterprise & Universal Forwarder
- Kali Linux (controlled attack simulation)
- Nmap (network reconnaissance)
- Windows command-line tools (`gpresult`, `whoami`, `net localgroup`)

---

## SOC & Security Workflow
- SOC-style authentication abuse investigation
- Identity threat triage and escalation reasoning
- Log collection and ingestion
- Detection logic development
- Policy validation and enforcement testing
- Evidence-based analysis and documentation
