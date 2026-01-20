🔐 Password Spray Detection Lab (RDP)
Lab Objective

Detect and analyze a password spray attack against a Windows host by correlating Windows Security Event Logs with Splunk SIEM. The goal was to identify failed authentication attempts across multiple user accounts originating from a single external host.

Lab Environment
Attacker

Kali Linux

Tooling:

Remmina (RDP client)

Nmap (port verification)

Target

Windows 11 Pro

Services:

Remote Desktop Protocol (RDP – TCP 3389)

Windows Security Auditing enabled

Local user accounts:

alice

bob

charlie

SOC

SIEM

Splunk Enterprise

Windows Security logs ingested via WinEventLog

Source type: WinEventLog:Security

Attack Simulation

Verified RDP availability on the Windows host:

nmap -Pn -p 3389 <windows_ip>


Performed multiple low-frequency RDP login attempts from Kali against different user accounts.

Ensured:

Only 1–2 attempts per account

Same source host (kali)

NTLM authentication failures

This behavior intentionally mimics a password spray, not a brute-force attack.

Detection & Analysis
Key Windows Event IDs
Event ID	Description
4625	Failed logon attempt
4776	Credential validation failure (NTLM)
Splunk Detection Queries
Failed Logons by Account (Primary Detection)
source="WinEventLog:Security" EventCode=4625
| stats count by Logon_Type, Account_Name


Result

Multiple user accounts targeted

Low failure count per account

Pattern consistent with password spraying

Source Attribution (Attacker Identification)
source="WinEventLog:Security" EventCode=4625
| table _time TargetUserName WorkstationName Source_Network_Address Logon_Type


Result

WorkstationName = kali

Same source IP across attempts

Confirms a single attacker host

Windows Event Viewer Validation
Event ID 4625 – Failed Logon

Observed fields:

TargetUserName: alice / bob / charlie

LogonType: 2 (Interactive) and 3 (Network)

WorkstationName: kali

AuthenticationPackage: NTLM

Event ID 4776 – Credential Validation Failure

Observed fields:

Authentication Package: NTLM

Logon Account: targeted user

Source Workstation: kali

Error Code: 0xC000006A (bad password)

These events confirm the authentication failures seen in Splunk are legitimate Windows security events.

Why This Is Password Spraying (Not Brute Force)
Indicator	Observation
Attempts per user	Low (1–2 attempts)
Number of users	Multiple
Source	Single host (Kali)
Timeframe	Short, controlled window

This aligns with password spraying, where attackers avoid account lockouts by spreading attempts across many accounts.

Outcome

Successfully generated realistic password spray authentication failures

Detected activity using Splunk SIEM

Correlated:

Attacker host → Windows logs → SIEM

Validated findings using raw Windows Event Viewer logs

Skills Demonstrated

Windows Security log analysis

Splunk search & correlation

Authentication failure analysis (NTLM)

Attack pattern classification

SOC-style investigation workflow

Screenshots (Referenced)

Screenshots are stored under:

screenshots/rdp-password-spray/


Key evidence includes:

Splunk failed logon statistics

Source workstation attribution

Event ID 4625 and 4776 validation

Kali RDP attempt evidence

Notes

This lab focuses on detection and analysis, not exploitation.
All activity was performed in a controlled lab environment for educational purposes.
