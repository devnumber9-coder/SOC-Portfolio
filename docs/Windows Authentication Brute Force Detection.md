# Windows Authentication Brute Force Detection (SOC-Home-Lab)

## Objective
Detect and analyze brute force authentication attempts on Windows systems using Splunk SIEM.

## Lab Environment
- **Attack Machine**: Kali Linux
- **Target**: Windows 10 with Splunk Universal Forwarder
- **SIEM**: Splunk Enterprise
- **Attack Tool**: Hydra (brute force tool)

## Skills Demonstrated
- Windows Event Log analysis (Event ID 4625)
- Splunk SPL query development
- Dashboard creation for security monitoring
- Attack simulation and detection

## Lab Setup

### Architecture
```
Kali Linux (Attacker) --> Windows 11 (Target with Forwarder) --> Splunk Enterprise (SIEM)
```

### Configuration Steps
1. Installed Splunk Universal Forwarder on Windows 10
2. Configured forwarder to collect Security logs
3. Set up receiving on Splunk Enterprise (port 9997)
4. Verified connectivity between all systems

## Attack Simulation

### Step 1: Baseline Testing
Tested network connectivity from Kali to Windows target:
```bash
ping 192.168.1.100
nmap -p 445 192.168.1.100
```

### Step 2: Brute Force Attack
Launched password spray attack using Hydra:
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt rdp://192.168.1.100
```

**Result**: Generated multiple failed login attempts (Event ID 4625)

## Detection & Analysis

### Event ID 4625 - Failed Logon
Windows logs failed authentication attempts with detailed information:
- Account name
- Source IP address
- Logon type
- Failure reason
- Timestamp

### Splunk Search Query
Basic detection query:
```spl
index=windows EventCode=4625
| stats count by src_ip, user
| where count > 5
```

Advanced query with time analysis:
```spl
index=windows EventCode=4625
| bucket _time span=1m
| stats count by _time, src_ip, user
| where count > 10
```

## Dashboard Creation

Created a dashboard with the following panels:

1. **Failed Logons Over Time** - Line chart showing attack patterns
2. **Top Failed Usernames** - Bar chart of targeted accounts
3. **Source IPs** - Table of attacking IP addresses
4. **Recent Failed Attempts** - Real-time log feed

### Dashboard SPL Examples

**Panel 1: Failed Logons Over Time**
```spl
index=windows EventCode=4625
| timechart count by src_ip
```

**Panel 2: Failed Logons by User**
```spl
index=windows EventCode=4625
| stats count by user
| sort -count
```

## Key Findings

- Detected 150+ failed login attempts within 5 minutes
- Attack originated from 192.168.1.50 (Kali machine)
- Targeted "administrator" and common usernames
- Clear pattern of automated brute force behavior

## Key Windows Security Event IDs

| Event ID | Meaning |
|----------|---------|
| 4625 | Failed logon attempt |
| 4624 | Successful logon |
| 4776 | Credential validation failure |

These events were used as the data sources for detection and visualization in this lab.

## Detection Improvements

### Alert Rule
Created alert to trigger on threshold:
```
Trigger: More than 10 failed logins from same IP within 5 minutes
Action: Send email notification
Severity: High
```

### Recommended Mitigations
1. Implement account lockout policies
2. Enable multi-factor authentication
3. Use strong password requirements
4. Monitor and alert on Event ID 4625
5. Block/rate-limit after repeated failures

## Screenshots

See `screenshots/SOC-Home/` for visual evidence:
- `kali-connectivity-test.png` - Network connectivity verification
- `win-event-4625.png` - Windows Event Viewer showing failed logons
- `splunk-search-4625.png` - Splunk search results
- `dashboard-overview.png` - Complete dashboard view
- `failed-logons-by-user.png` - Username analysis chart

## Lessons Learned

1. **Log Forwarding**: Proper configuration of Universal Forwarder is critical for real-time detection
2. **Baseline Knowledge**: Understanding normal authentication patterns helps identify anomalies
3. **SPL Efficiency**: Using stats and timechart makes pattern detection more effective
4. **Dashboard Design**: Visual representation accelerates threat identification
5. **Threshold Tuning**: Balance between false positives and true attack detection

## Tools Used
- Splunk Enterprise 10
- Splunk Universal Forwarder
- Kali Linux
- Windows Event Viewer
- Nmap

## References
- Microsoft Event ID 4625 Documentation
- Splunk SPL Documentation
- MITRE ATT&CK T1110 (Brute Force)
- 
## Conclusion

This lab demonstrated how to collect Windows authentication logs, simulate failed logon activity, and visualize patterns using dashboards in Splunk. While alerting thresholds may vary in production environments, the dashboard provides meaningful analyst visibility into authentication trends that could indicate brute force or related suspicious behavior.

---

**Lab Completion Date**: January 2026  
**Total Time**: 4 hours
