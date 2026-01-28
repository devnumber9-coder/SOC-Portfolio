SOC Portfolio
=============

This repository contains hands-on **Security Operations (SOC)** lab work designed to demonstrate practical skills in log analysis, detection engineering, SIEM investigation using Splunk, Windows authentication monitoring, and Active Directory security fundamentals.

All labs are built and documented in controlled lab environments with a focus on defensive security, investigation, and validation rather than exploitation.

Completed Labs
--------------

[Windows Authentication Brute Force Detection](https://github.com/devnumber9-coder/SOC-Portfolio/blob/main/docs/Windows-Authentication-Brute-Force-Detection.md)  
  - Detection of high-volume failed logon activity using Windows Event ID 4625  
  - Threshold-based alerting and correlation in Splunk  

[Password Spray Detection](https://github.com/devnumber9-coder/SOC-Portfolio/blob/main/docs/Password-Spray-Lab.md)  
  - Identification of low-and-slow authentication abuse across multiple user accounts  
  - Time-binned analysis and alerting using Splunk SPL  

[Active Directory Workstation Hardening & GPO Baseline](https://github.com/devnumber9-coder/SOC-Portfolio/blob/main/docs/Active-Directory-Workstation-GPO-Baseline.md)  
  - Domain controller and workstation deployment  
  - OU design, user and computer separation  
  - Group Policy enforcement for workstation security  
  - Local administrator privilege control using Restricted Groups  
  - Validation of authentication, authorization, and policy application
  
[Kerberos Abuse Detection & SOC Correlation](https://github.com/devnumber9-coder/SOC-Portfolio/blob/main/docs/Kerberos-Abuse-Detection-Correlation-Lab)  
- Analysis of suspicious Kerberos service ticket activity (Event ID 4769)  
- Correlation of Kerberos authentication (4768), service tickets (4769), successful logons (4624), and privileged sessions (4672)  
- Demonstrated how Kerberos abuse may occur without failed logon events (4625)  
- SOC-style investigation using event sequencing, frequency analysis, and session context  
- MITRE ATT&CK mapping: T1558 (Steal or Forge Kerberos Tickets)

In Progress / Future Labs
-------------------------

* Lateral Movement Detection (Windows Event Logs)  
* Privileged Account Monitoring (Active Directory)  
* Identity-Based Threat Correlation in Splunk  

Skills Summary
--------------

See [SKILLS.md](https://github.com/devnumber9-coder/SOC-Portfolio/blob/main/SKILLS.md)

How to Use This Repo
--------------------
This portfolio emphasizes identity-based threat detection, including Windows authentication monitoring and Kerberos abuse detection from a SOC analyst perspective.

Each lab is self-contained:
*  `docs/` contains the written lab documentation  
*  `screenshots/` contains corresponding images  
*  Individual .md files explain objectives, steps, results, and lessons  
