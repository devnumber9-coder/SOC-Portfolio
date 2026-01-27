# Windows Authentication Brute Force Detection Lab (Splunk Home Lab)

## Objective
Detect and analyze brute force authentication activity against Windows endpoints using Windows Security Event Logs and Splunk SIEM.

## Lab Environment
- **Attack Machine**: Kali Linux
- **Target**: Windows 11 Pro with Splunk Universal Forwarder
- **SIEM**: Splunk Enterprise
- **Attack Surface**: Remote Desktop Protocol (RDP)
- **Network**: VirtualBox Host-Only Adapter (192.168.56.0/24)

## Skills Demonstrated
- Windows Event Log analysis (Event IDs 4625, 4776)
- Splunk SPL query development
- Brute force detection logic
- SOC-style investigation workflow
- Alert creation for security monitoring

## Lab Setup

### Architecture
```
Kali Linux (Attacker) --> Windows 11 (Target with Forwarder) --> Splunk Enterprise (SIEM)
```

### Configuration Steps
1. Installed Splunk Universal Forwarder on Windows 11
2. Configured forwarder to collect Security logs
3. Set up receiving on Splunk Enterprise
4. Created local Windows accounts (alice, bob, charlie, SOC)
5. Enabled Remote Desktop Protocol (TCP/3389)
6. Configured Host-Only network (ICMP blocked, RDP accessible)

## Attack Simulation

### Step 1: Verify Log Ingestion
Confirmed Splunk was actively ingesting Windows Security events before generating attack traffic:
- Verified Security logs present in Windows Event Viewer
- Confirmed events appeared in Splunk using basic search query

Basic ingestion verification query:
```spl
source="WinEventLog:Security"
```

This step ensured proper log forwarding and real-time detection capability before simulating attack activity.

### Step 2: Brute Force Attack Simulation
Simulated brute force attack by attempting authentication using:
- **Multiple incorrect passwords**
- **Single target user account** (alice)
- **A single source system** (Kali)
- **Repeated attempts in rapid succession**

**Result**: Generated high-volume failed authentication events (Event IDs 4625, 4776)

**Note**: This technique targets a single account with many attempts, differs from password spray attacks which target multiple accounts with few attempts per user.

## Detection & Analysis

### Event ID 4625 - Failed Logon
Windows logs failed authentication attempts with detailed information:
- Account name (TargetUserName)
- Source IP address (Source Network Address)
- Logon type (3 = Network, 10 = RemoteInteractive)
- Workstation name
- Authentication package (NTLM)
- Failure reason (Invalid credentials)

### Event ID 4776 - Credential Validation Failure
Supporting evidence for authentication failures:
- Authentication Package: NTLM
- Logon Account: alice / bob / charlie
- Source Workstation: kali
- Status Codes: Bad password failures

Event ID 4776 was used as **supporting evidence** to validate authentication failures observed in Event ID 4625.

### Splunk Search Query

Basic detection query:
```spl
source="WinEventLog:Security" EventCode=4625
| stats count by Account_Name
| sort - count
```

Username normalization query:
```spl
source="WinEventLog:Security" EventCode=4625
| eval user=coalesce(TargetUserName, Account_Name)
| stats count by user
| sort - count
```

Time-based brute force detection:
```spl
source="WinEventLog:Security" EventCode=4625
| eval user=coalesce(TargetUserName, Account_Name)
| bin _time span=1m
| stats count as failures by _time, user
| sort _time - failures
```

**Note on Field Normalization**: The `coalesce()` function was used to handle cases where `TargetUserName` or `Account_Name` fields were blank or null, ensuring consistent username detection across different event formats.

## Alert Creation

### Detection Logic
```spl
source="WinEventLog:Security" EventCode=4625
| eval user=coalesce(TargetUserName, Account_Name)
| bin _time span=1m
| stats count as failures by _time, user
| where failures >= 10
| sort _time - failures
```

### Alert Configuration
- **Name**: Brute Force Authentication Detection
- **Type**: Scheduled alert
- **Schedule**: Every 5 minutes (Cron: `*/5 * * * *`)
- **Time Range**: Last 5 minutes
- **Trigger Condition**: If results > 0
- **Action**: Log and flag suspicious authentication activity
- **Severity**: Critical

## Key Findings

- Single user account experienced high-volume failed authentication attempts
- All attempts originated from the same source system (Kali)
- Failures occurred within a concentrated time window (burst pattern)
- High attempt count per user (10+ failures per minute)
- Pattern aligns with brute force attack, not password spray

## Key Windows Security Event IDs

| Event ID | Meaning |
|----------|---------|
| 4625 | Failed logon attempt |
| 4776 | Credential validation failure |
| 4624 | Successful logon |

These events were used as the data sources for detection and analysis in this lab.

## Analysis

Indicators supporting brute force classification:
1. High attempt count per user (10+ per minute)
2. Single account targeted
3. Single source host
4. Rapid, sequential time pattern
5. NTLM authentication failures

Analyst would escalate this activity for:
- Account compromise investigation
- Source host identification and containment
- Password reset and credential rotation
- Potential account lockout implementation

## Recommended Mitigations
1. Implement account lockout policies
2. Enable multi-factor authentication
3. Use strong password requirements
4. Monitor and alert on Event IDs 4625 and 4776
5. Restrict RDP access to authorized IPs
6. Deploy rate-limiting for authentication attempts

## Screenshots

See `screenshots/rdp-brute-force/` for visual evidence:
- Splunk ingestion baseline showing Security events
- Windows Event Viewer showing Event ID 4625 with attacked user and source workstation
- Splunk search showing failures by user (normalized with coalesce)
- Time-bucketed failures showing burst pattern
- Triggered alert in Splunk

## Lessons Learned

1. **Field Normalization**: Using `coalesce()` in Splunk ensures consistent username detection when multiple field names exist in event data
2. **Attack Pattern Recognition**: Brute force differs from password spray through high-volume attempts against a single account versus low attempts across multiple accounts
3. **Event Correlation**: Combining Event IDs 4625 and 4776 provides stronger detection confidence
4. **Threshold Tuning**: Balance between false positives and true attack detection based on environment (10+ failures per minute used for this lab)
5. **Time Range Selection**: Using appropriate time ranges ("Last 60 minutes") prevents mixing old lab data with new attack traffic

## Tools Used
- Splunk Enterprise
- Splunk Universal Forwarder
- Kali Linux
- Windows Event Viewer
- VirtualBox

## References
- Microsoft Event ID 4625 Documentation
- Microsoft Event ID 4776 Documentation
- Splunk SPL Documentation
- MITRE ATT&CK T1110.001 (Password Guessing)

## Conclusion

This lab demonstrated how to collect Windows authentication logs, simulate brute force activity, and detect patterns using Splunk SIEM. The focus was on detection, correlation, and alerting from a analyst perspective. The brute force detection logic provides meaningful visibility into authentication anomalies that could indicate credential abuse or account compromise attempts.

---

**Lab Completion Date**: January 2026  
**Total Time**: 4 hours
