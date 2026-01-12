# Brute Force Incident Report

## Incident Overview

Multiple failed authentication attempts were detected against a Windows 11 endpoint, indicating potential brute-force activity.

Detection:
- Source: Windows Security Event Log
- Indicator: Event ID 4625 (failed logon) Local account
- Pattern: Repeated failures within a short time window
- 
## Timeline

- System Event log Securtly logged Event ID 4625 (failed Logon) attempts from local machine
- Ststem Event log Event ID 4625  (failed Logon) 5 or more failed event triggered ststem timeout



## Detection


## Response and Containment

Impact:
- No successful compromise observed
- Account remained accessible only after valid credentials were used


## Lessons Learned

Recommended Remediation:
- Configure account lockout thresholds
- Implement alerting for repeated authentication failures wihtou succesful logins
- Review source of authentication attempts
