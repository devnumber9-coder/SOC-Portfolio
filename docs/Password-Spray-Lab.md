# Password Spray Detection Lab (Windows Authentication Monitoring)

## Overview
This lab demonstrates how a **SOC analyst detects password spray activity** against a Windows endpoint using **Windows Security Event Logs** and **Splunk SIEM**.  

The focus of this lab is **detection, correlation, and alerting**, not exploitation.  
All activity was performed in a controlled lab environment.

---

## Objectives
- Simulate password spray behavior against multiple Windows user accounts
- Observe authentication failures in native Windows logs
- Ingest and analyze those logs in Splunk
- Build detection logic to identify password spraying patterns
- Create a basic SOC alert based on authentication anomalies

---

## Lab Environment

### Attacker System
- **OS:** Kali Linux
- **Role:** Simulated attacker
- **Method:** RDP authentication attempts
- **Network:** VirtualBox Host-Only Adapter

### Target System
- **OS:** Windows 11 Pro
- **Service:** Remote Desktop Protocol (RDP)
- **Local Accounts:**
  - `alice`
  - `bob`
  - `charlie`
- **Logging:** Windows Security Event Log enabled

### SIEM
- **Platform:** Splunk Enterprise
- **Log Source:** `WinEventLog:Security`
- **Collection Method:** Splunk Universal Forwarder

---

## Network Architecture
The lab was conducted using a **Host-Only VirtualBox network** to simulate an **internal attacker scenario** and ensure reliable communication between the attacker and target systems.

- Both systems assigned IPs in the `192.168.56.0/24` range
- ICMP (ping) traffic blocked
- TCP/3389 (RDP) accessible from Kali

This configuration mirrors common enterprise environments where ICMP is restricted but application services remain reachable.

---

## Attack Simulation – Password Spraying

### Technique
Password spraying was simulated by attempting authentication using:
- **One incorrect password**
- **Multiple valid user accounts**
- **A single source system (Kali)**
- **One attempt per account**

This technique avoids account lockouts and differs from brute-force attacks, which repeatedly target a single account.

### Accounts Targeted
- `alice`
- `bob`
- `charlie`

### Attack Surface
- Remote Desktop Protocol (RDP)
- TCP port 3389

---

## Windows Event Evidence

During the attack simulation, Windows generated authentication-related security events.

### Event ID 4625 – Failed Logon
Observed fields included:
- **TargetUserName:** alice / bob / charlie
- **LogonType:** 3 (Network) or 10 (RemoteInteractive)
- **WorkstationName:** kali
- **Source Network Address:** Kali IP
- **Authentication Package:** NTLM
- **Failure Reason:** Invalid credentials

### Event ID 4776 – Credential Validation Failure
Observed fields included:
- **Authentication Package:** NTLM
- **Logon Account:** alice / bob / charlie
- **Source Workstation:** kali
- **Status Codes:** Bad password failures

Event ID 4776 was used as **supporting evidence** to validate authentication failures observed in Event ID 4625.

---

## Splunk Detection & Analysis

### Raw Failed Logons
```spl
source="WinEventLog:Security" EventCode=4625

Failed Logons by Account
source="WinEventLog:Security" EventCode=4625
| stats count by Account_Name

Source Attribution
source="WinEventLog:Security" EventCode=4625
| stats count by Source_Network_Address, Workstation_Name

Time-Based Password Spray Detection
source="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count by _time, Source_Network_Address
| where count >= 3

Observations

Multiple user accounts failed authentication

All attempts originated from the same source system (Kali)

Failures occurred within a short time window

Pattern aligns with password spraying, not brute force

Alert Creation
Detection Logic
source="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count as failures by Source_Network_Address, Workstation_Name
| where failures >= 3

Alert Configuration

Name: Password Spray Detection – Windows Authentication

Type: Scheduled alert

Schedule: Every 5 minutes

Time Range: Last 5 minutes

Trigger Condition: If results > 0

Action: Log and flag suspicious authentication activity

Severity: Medium

SOC Analysis

Indicators supporting password spray classification:

Low attempt count per user

Multiple accounts targeted

Single source host

Short, clustered time window

NTLM authentication failures

A SOC analyst would escalate this activity for:

Credential abuse investigation

Source host review

Potential containment actions

Skills Demonstrated

Windows authentication log analysis

Event ID correlation (4625, 4776)

Splunk SPL development

Password spray detection logic

SIEM alert creation

SOC-style investigation workflow

Screenshots

Screenshots are stored under:

screenshots/rdp-password-spray/
