# Detection and Alerts

## SIEM Log Ingestion (Splunk)

Windows 11 Security Event Logs were successfully forwarded to Splunk SIEM using the Splunk Universal Forwarder.

Validated ingestion:
- Security Event Log are indexed in Splunk for searches
- Failed logon events (Event ID 4625) searchable via Splunk queries

This will enable centralized monitoring and investigation of authentication activity.
