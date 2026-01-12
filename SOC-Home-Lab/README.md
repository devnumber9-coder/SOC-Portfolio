# SOC Home Lab – Brute Force Authentication Detection

This project demonstrates the design, implementation, and validation of a basic SOC detection pipeline using Windows endpoint logs and Splunk Enterprise.

The lab focuses on detecting brute-force authentication attempts through Windows Security Event Logs and validating detection logic using SIEM searches, scheduled jobs, and analysis of endpoint behavior.

## Objectives
- Simulate suspicious authentication behavior
- Collect and ingest endpoint security logs
- Build and validate detection logic in Splunk
- Analyze alert behavior and detection limitations
- Document findings using SOC-style methodology

## Tools Used
- Windows 11 (Endpoint)
- Kali Linux (Attack simulation)
- Splunk Enterprise (SIEM)
- Windows Event Viewer

## Key Takeaways
- Endpoint lockout behavior can affect alert triggering
- Detection validation does not always rely on alert firing
- Dashboard-based analysis is often more reliable for authentication signals
