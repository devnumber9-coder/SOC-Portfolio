# Password Spray Detection Lab (Splunk Home Lab)

## Objective
Detect and analyze password spray activity against Windows endpoints using Windows Security Event Logs and Splunk SIEM.

## Lab Environment
- **Attack Machine**: Kali Linux
- **Target**: Windows 11 Pro with Splunk Universal Forwarder
- **SIEM**: Splunk Enterprise
- **Attack Surface**: Remote Desktop Protocol (RDP)
- **Network**: VirtualBox Host-Only Adapter (192.168.56.0/24)

## Skills Demonstrated
- Windows Event Log analysis (Event IDs 4625, 4776)
- Splunk SPL query development
- Password spray detection logic
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
4. Created local Windows accounts (alice, bob, charlie)
5. Enabled Remote Desktop Protocol (TCP/3389)
6. Configured Host-Only network (ICMP blocked, RDP accessible)

## Attack Simulation

### Step 1: Network Testing
Verified connectivity from Kali to Windows target:
- Both systems assigned IPs in 192.168.56.0/24 range
- ICMP (ping) traffic blocked
- TCP/3389 (RDP) accessible from Kali

This configuration mirrors enterprise environments where ICMP is restricted but application services remain reachable.

### Step 2: Password Spray Attack
Simulated password spray by attempting authentication using:
- **One incorrect password**
- **Multiple valid user accounts** (alice, bob, charlie)
- **A single source system** (Kali)
- **One attempt per account**

**Result**: Generated failed authentication events (Event IDs 4625, 4776)

**Note**: This technique avoids account lockouts and differs from brute-force attacks, which repeatedly target a single account.

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
```

Source attribution analysis:
```spl
source="WinEventLog:Security" EventCode=4625
| stats count by Source_Network_Address, Workstation_Name
```

Time-based password spray detection:
```spl
source="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count by _time, Source_Network_Address
| where count >= 3
```

## Alert Creation

### Detection Logic
```spl
source="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count as failures by Source_Network_Address, Workstation_Name
| where failures >= 3
```

### Alert Configuration
- **Name**: Password Spray Detection – Windows Authentication
- **Type**: Scheduled alert
- **Schedule**: Every 5 minutes
- **Time Range**: Last 5 minutes
- **Trigger Condition**: If results > 0
- **Action**: Log and flag suspicious authentication activity
- **Severity**: Medium

## Key Findings

- Multiple user accounts failed authentication
- All attempts originated from the same source system (Kali)
- Failures occurred within a short time window
- Low attempt count per user (avoids lockouts)
- Pattern aligns with password spraying, not brute force

## Key Windows Security Event IDs

| Event ID | Meaning |
|----------|---------||
| 4625 | Failed logon attempt |
| 4776 | Credential validation failure |
| 4624 | Successful logon |

These events were used as the data sources for detection and analysis in this lab.

## SOC Analysis

Indicators supporting password spray classification:
1. Low attempt count per user
2. Multiple accounts targeted
3. Single source host
4. Short, clustered time window
5. NTLM authentication failures

A SOC analyst would escalate this activity for:
- Credential abuse investigation
- Source host review
- Potential containment actions

## Recommended Mitigations
1. Implement account lockout policies
2. Enable multi-factor authentication
3. Use strong password requirements
4. Monitor and alert on Event IDs 4625 and 4776
5. Restrict RDP access to authorized IPs
6. Deploy rate-limiting for authentication attempts

## Screenshots

See `screenshots/rdp-password-spray/` for visual evidence:
- Network connectivity verification
- Windows Event Viewer showing failed logons
- Splunk search results
- Alert configuration
- Event correlation analysis

## Lessons Learned

1. **Log Forwarding**: Proper configuration of Universal Forwarder is critical for real-time detection
2. **Attack Pattern Recognition**: Password spraying differs from brute force through targeting multiple accounts with low attempts per user
3. **Event Correlation**: Combining Event IDs 4625 and 4776 provides stronger detection confidence
4. **Threshold Tuning**: Balance between false positives and true attack detection based on environment
5. **Network Segmentation**: Host-only networks effectively simulate internal threat scenarios

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
- MITRE ATT&CK T1110.003 (Password Spraying)

## Conclusion

This lab demonstrated how to collect Windows authentication logs, simulate password spray activity, and detect patterns using Splunk SIEM. The focus was on detection, correlation, and alerting from a SOC analyst perspective. While alerting thresholds may vary in production environments, the detection logic provides meaningful visibility into authentication anomalies that could indicate password spraying or related suspicious behavior.

---

**Lab Completion Date**: January 2026  
**Total Time**: 4 hours
