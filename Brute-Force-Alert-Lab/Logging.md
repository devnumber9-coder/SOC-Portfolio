## Logging

Data Sources:
- Windows Security Event Logs (Event ID 4625 – Failed Logon)
- Endpoint: Windows 11 VM
- Collection Method: Splunk Universal Forwarder

Configuration:
- Forwarder configured to monitor Security logs
- Events forwarded to Splunk indexer in real time
- Index: `windows_security`