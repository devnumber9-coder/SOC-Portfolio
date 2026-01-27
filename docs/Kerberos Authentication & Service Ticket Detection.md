# Kerberos Authentication & Service Ticket Detection Lab

## Overview

This lab focuses on understanding, validating, and detecting Kerberos authentication activity within an Active Directory environment from a analyst perspective. Rather than exploitation, the emphasis is on log interpretation, event correlation, and detection logic using native Windows Security logs.

The lab walks through normal Kerberos behavior and contrasts it with suspicious patterns that may indicate credential abuse, lateral movement, or password spraying.

## Lab Objectives

- Observe Kerberos authentication flow in Windows Security logs
- Understand the relationship between 4768 (TGT) and 4769 (Service Ticket) events
- Correlate Kerberos events with logon activity (4624, 4634, 4672)
- Identify SOC-relevant Kerberos detection patterns
- Document findings in an analyst-style format

## Lab Environment

| Component | Details |
|-----------|----------|
| Domain Controller | Windows Server (AD DS, DNS) |
| Client | Windows 10/11 domain-joined workstation |
| Authentication | Kerberos (default AD auth) |
| Logs | Windows Security Event Log |
| Network | Internal home lab |

## Key Kerberos Events

| Event ID | Meaning |
|----------|----------|
| 4768 | Kerberos Authentication Ticket (TGT) requested |
| 4769 | Kerberos Service Ticket requested |
| 4624 | Successful logon |
| 4634 | Logoff |
| 4672 | Special privileges assigned |

## Kerberos Flow (Baseline)

### Step 1 — TGT Request (4768)

- Generated when valid credentials are presented
- Issued by the KDC (Domain Controller)
- Service name: krbtgt

** 4768 confirms initial authentication success, not resource access.

### Step 2 — Service Ticket Request (4769)

- Generated when a user requests access to a service (e.g., DC, file share)
- Occurs after TGT issuance

** 4769 reveals what service is being accessed, from where, and by whom.

### Step 3 — Logon Context Events

Observed alongside Kerberos activity:

- 4624 — session created
- 4672 — elevated privileges (admins, services)
- 4634 — session terminated

These events provide session context, not Kerberos mechanics.

## Detection Patterns 
### 1. Normal Kerberos Behavior (Baseline)

**Pattern**: 4768 → 4769 → 4624 → 4634

**Interpretation**: Normal interactive or service-based authentication

### 2. Repeated 4769 Events

**Pattern**: High volume of 4769 events, same user, same client, multiple services

**Possible Indicators**:
- Service enumeration
- Automated access attempts
- Lateral movement

### 3. 4769 Without New 4768

**Pattern**: Service tickets issued without a recent TGT request

**Possible Indicators**:
- Ticket reuse
- Pass-the-Ticket behavior

### 4. Password Spray Context

**Observed Behavior**:
- 4624 / 4634 present
- 4769 present
- No repeated 4768 failures

**Explanation**: Password spraying may succeed silently. Kerberos failures are not always noisy.

**Lesson**: Kerberos logs alone may not detect sprays — context is required.

## Key Takeaways

- **4768 ≠ logon**: It confirms credential validation
- **4769 ≠ authentication**: It confirms service access
- **Kerberos detection requires**: Sequence + frequency analysis
- **Many attacks blend into**: Normal Kerberos noise

## Skills Demonstrated

- Active Directory authentication analysis
- Kerberos event interpretation
- Windows Security log correlation
- Clear technical documentation

## Screenshots Included

- ✓ 4768 TGT request (successful authentication)
- ✓ 4769 service ticket requests
- ✓ Event sequence showing normal Kerberos flow
- ✓ Repeated 4769 events
- ✓ Correlation with logon and privilege events

## Analysis

Indicators for Kerberos anomaly detection:

1. **High 4769 frequency** from single source
2. **Missing 4768 preceding 4769** (ticket reuse)
3. **Multiple service tickets to unusual targets** (lateral movement)
4. **4769 failures with specific error codes** (permission or trust issues)
5. **Pattern correlation with 4625 failures** (brute force + Kerberos)

An analyst would escalate this activity for:

- Account compromise investigation
- Lateral movement detection
- Privileged account monitoring
- Service compromise assessment

## Recommended Detections

1. Alert on 4769 without preceding 4768 (potential pass-the-ticket)
2. Monitor for rapid 4769 sequences (service enumeration)
3. Track 4769 to unusual services (command execution attempts)
4. Correlate 4768 failures with 4769 success (attack patterns)
5. Detect 4769 from non-user accounts to sensitive services

## Lessons Learned

1. **Kerberos Events Are Noisy**: High volume of normal activity requires baseline understanding
2. **Context Is Critical**: 4768/4769 alone don't indicate compromise; sequence matters
3. **Service Tickets Reveal Intent**: The target service in 4769 indicates what attacker is accessing
4. **Timestamp Correlation**: Time between 4768 and 4769 can indicate attack vs. normal use
5. **Privilege Events Matter**: Combine 4672 with Kerberos activity for privilege escalation detection

## Tools Used

- Windows Event Viewer
- Splunk SIEM (or equivalent log analysis tool)
- Active Directory
- PowerShell for event log queries

## References

- Microsoft Event ID 4768 Documentation
- Microsoft Event ID 4769 Documentation
- MITRE ATT&CK T1550 (Use Alternate Authentication Material)
- MITRE ATT&CK T1558 (Steal or Forge Kerberos Tickets)

## Conclusion

This lab demonstrated how Kerberos authentication appears in enterprise logs and how an analyst can distinguish normal identity behavior from potential abuse. The focus on detection logic and reasoning mirrors real-world workflows and prepares analysts to investigate identity-based alerts effectively.

**Lab Completion Date**: January 2026
**Total Time**: 3 hours
