# Splunk Dashboard Lab – Failed Authentication Monitoring

This project demonstrates the creation and deployment of a SOC dashboard for monitoring failed authentication attempts in a Windows environment using Splunk Enterprise.

The dashboard provides real-time visibility into authentication failures, enabling analysts to identify potential brute-force attacks, password spray attempts, and compromised accounts.

## Objectives
- Build a functional SOC dashboard for authentication monitoring
- Ingest and analyze Windows Security Event ID 4625 (Failed Logon)
- Simulate attack scenarios to validate dashboard detection capabilities
- Document SOC analyst workflow for authentication failure triage

## Tools Used
- Splunk Enterprise (SIEM)
- Windows 11 (Endpoint)
- Windows Security Event Logs

---

## Attack Simulation

To generate realistic authentication failures, multiple failed logon attempts were performed against a Windows 11 host using invalid credentials.

The activity generated Windows Security Event ID 4625 (Failed Logon), which was ingested into Splunk in near real-time.

This simulates:
- Local brute-force attempts
- Repeated authentication failures against a single account
- Short-burst attack behavior commonly seen in real environments

**Why this matters:**
Shows you didn't just "click buttons" — explains why the data exists.

---

## Dashboard Components

The dashboard includes the following panels:

1. **Failed Logon Timeline** – Visualizes authentication failures over time
2. **Top Accounts with Failed Logons** – Identifies accounts experiencing abnormal failure volume
3. **Failed Logon Count by User** – Table showing failure counts per account
4. **Recent Failed Logon Events** – Displays the most recent Event ID 4625 entries

### Search Query Used

```spl
index=windows_security EventCode=4625
| stats count by Account_Name
| sort -count
```

---

## Detection Logic Explained

The detection focuses on identifying repeated failed authentication attempts within a short time window.

**Key logic:**
- Filters Windows Security logs for Event ID 4625
- Aggregates failures by user account
- Highlights accounts experiencing abnormal failure volume

This approach mirrors real SOC detections used to identify brute-force and password guessing activity.

**This proves:**
You understand what your query does — you're not copy-pasting blindly.

---

## SOC Analyst Workflow

In a SOC environment, this dashboard would be used as follows:

1. Analyst monitors failed logon trends over time
2. Sudden spikes indicate potential brute-force activity
3. Analyst identifies affected user accounts
4. Correlates failed and successful logons to assess compromise risk
5. Escalates suspicious activity for investigation or containment

Dashboards provide visibility and context before alerts or automation are applied.

**This shows:**
You think like an analyst — you understand SOC process, not just tools.

---

## Limitations and Future Improvements

### Current Limitations
- Single-host environment
- No source IP enrichment
- No automated response actions

### Planned Improvements
- Add password spraying detection
- Correlate Event ID 4776 for credential validation
- Add host and IP-based panels
- Implement threshold-based alerts after baseline tuning

---

## Key Takeaways

- Dashboards are essential for SOC visibility before implementing automated alerting
- Failed authentication monitoring is a foundational security control
- Understanding detection logic is more valuable than generating alerts in a lab
- Real SOC workflows emphasize context and correlation over raw event counts

---

## Evidence

Screenshots and additional documentation:
- Dashboard panels (visual layout)
- Search query results
- Event ID 4625 samples from Splunk
