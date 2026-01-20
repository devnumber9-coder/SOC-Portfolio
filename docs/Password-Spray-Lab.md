# Password Spray Attack Detection (SOC-Home-Lab)

## Objective
Detect and analyze password spray attacks against Windows RDP services by correlating Windows Security Event Logs with Splunk SIEM. Identify failed authentication attempts across multiple user accounts originating from a single external host.

## Lab Environment
- **Attack Machine**: Kali Linux
- **Target**: Windows 11 Pro with Windows Security Auditing
- **SIEM**: Splunk Enterprise
- **Attack Tool**: Remmina (RDP client)
- **Service**: Remote Desktop Protocol (RDP - TCP 3389)

## Skills Demonstrated
- Windows Event Log analysis (Event IDs 4625, 4776)
- Splunk SPL query development
- Attack pattern classification (password spray vs brute force)
- NTLM authentication failure analysis
- SOC-style investigation workflow

## Lab Setup

### Architecture
```
Kali Linux (Attacker) --> Windows 11 Pro (Target with RDP) --> Splunk Enterprise (SIEM)
```

### Configuration Steps
1. Configured Windows Security Auditing on Windows 11 Pro
2. Created local user accounts: alice, bob, charlie
3. Enabled Remote Desktop Protocol (TCP 3389)
4. Set up Splunk Enterprise to ingest Windows Security logs via WinEventLog
5. Verified connectivity between all systems

## Attack Simulation

### Step 1: Baseline Testing
Verified RDP availability on the Windows host:
```bash
nmap -Pn -p 3389 <windows_ip>
```

### Step 2: Password Spray Attack
Performed multiple low-frequency RDP login attempts from Kali against different user accounts:
- 1-2 attempts per account
- Same source host (kali)
- NTLM authentication failures

**Attack Characteristics**:
- Multiple user accounts targeted (alice, bob, charlie)
- Low attempt count per user (avoids account lockout)
- Single attacking host
- Controlled timeframe

**Result**: Generated failed authentication events (Event IDs 4625 and 4776) consistent with password spraying behavior

## Detection & Analysis

### Event ID 4625 - Failed Logon
Windows logs failed authentication attempts with detailed information:
- Account name (TargetUserName)
- Source workstation (WorkstationName: kali)
- Source IP address (Source_Network_Address)
- Logon type (2: Interactive, 3: Network)
- Authentication package (NTLM)

### Event ID 4776 - Credential Validation Failure
NTLM-specific authentication failures provide:
- Authentication Package: NTLM
- Logon Account: targeted user
- Source Workstation: kali
- Error Code: 0xC000006A (bad password)

### Splunk Search Queries

**Primary Detection: Failed Logons by Account**
```spl
source="WinEventLog:Security" EventCode=4625
| stats count by Logon_Type, Account_Name
```

**Source Attribution: Attacker Identification**
```spl
source="WinEventLog:Security" EventCode=4625
| table _time TargetUserName WorkstationName Source_Network_Address Logon_Type
```

**Advanced Detection with NTLM Analysis**
```spl
source="WinEventLog:Security" EventCode=4776
| stats count by Logon_Account, Workstation
| where count > 1
```

## Key Findings

- Multiple user accounts targeted (alice, bob, charlie)
- Low failure count per account (1-2 attempts)
- All attacks originated from single workstation: kali
- NTLM authentication failures with error code 0xC000006A
- Attack pattern consistent with password spraying technique

## Password Spray vs Brute Force

### Attack Classification

| Indicator | Password Spray | Brute Force |
|-----------|----------------|-------------|
| Attempts per user | Low (1-2 attempts) | High (many attempts) |
| Number of users | Multiple accounts | Single account |
| Source | Single host | Can be distributed |
| Timeframe | Short, controlled | Rapid, sustained |
| Goal | Avoid lockout | Crack password |

**This Lab**: Pattern aligns with password spraying, where attackers avoid account lockouts by spreading attempts across many accounts.

## Key Windows Security Event IDs

| Event ID | Meaning |
|----------|---------|
| 4625 | Failed logon attempt |
| 4776 | Credential validation failure (NTLM) |
| 4624 | Successful logon |

These events were used as the data sources for detection and correlation in this lab.

## Detection Improvements

### Alert Rule
Created alert to trigger on password spray pattern:
```
Trigger: 3+ different accounts with failed logins from same source within 10 minutes
Action: Send email notification
Severity: Medium-High
```

### Recommended Mitigations
1. Implement account lockout policies (balance with spray detection)
2. Enable multi-factor authentication for RDP
3. Use network-level authentication (NLA)
4. Monitor and alert on Event IDs 4625 and 4776
5. Restrict RDP access via firewall/VPN
6. Deploy honeypot accounts to detect spraying
7. Implement password complexity requirements

## Screenshots

See `screenshots/rdp-password-spray/` for visual evidence:
- `nmap-rdp-verification.png` - RDP port availability check
- `kali-rdp-attempt.png` - Remmina login attempts from Kali
- `splunk-failed-logons.png` - Splunk statistics by account
- `splunk-source-attribution.png` - Source workstation identification
- `event-4625-details.png` - Windows Event Viewer failed logon
- `event-4776-ntlm.png` - NTLM credential validation failure

## Lessons Learned

1. **Attack Pattern Recognition**: Understanding the difference between password spray and brute force attacks is critical for proper response
2. **Event Correlation**: Combining Event IDs 4625 and 4776 provides complete authentication failure context
3. **Low-and-Slow Detection**: Password spray attacks require different detection thresholds than brute force
4. **Source Attribution**: WorkstationName field is valuable for identifying attacking hosts in domain environments
5. **NTLM Analysis**: Error codes (0xC000006A) help confirm bad password vs other authentication issues

## Tools Used
- Splunk Enterprise
- Windows Event Viewer
- Kali Linux
- Remmina (RDP client)
- Nmap

## References
- Microsoft Event ID 4625 Documentation
- Microsoft Event ID 4776 Documentation
- Splunk SPL Documentation
- MITRE ATT&CK T1110.003 (Password Spraying)

## Conclusion

This lab demonstrated how to detect password spray attacks against RDP services using Windows Security Event Logs and Splunk SIEM. The key distinction from brute force attacks is the low attempt count per account across multiple users, designed to avoid account lockout policies. By correlating Event IDs 4625 and 4776, we successfully identified the attacking host and validated the password spray pattern through both Splunk queries and raw Windows Event Viewer analysis.

---

**Lab Completion Date**: January 2026  
**Total Time**: 3 hours