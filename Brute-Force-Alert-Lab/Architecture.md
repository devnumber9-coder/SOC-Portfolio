## Architecture

Lab Environment:
- **Attacker:** Kali Linux VM (Host-only adapter)
- **Target:** Windows 11 VM (Host-only adapter)
- **SIEM:** Splunk Enterprise (running on Windows 11 VM)

Network Configuration:
- Host-only network isolates lab traffic
- Static IPs assigned to both endpoints
- Firewall rules allow ICMP and SMB traffic

Log Flow:
- Windows Security Event Logs → Splunk Universal Forwarder → Splunk Indexer