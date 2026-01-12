## Lab Architecture

The environment consists of:
- A Windows 11 host generating authentication logs
- A Kali Linux VM used for initial network validation
- Splunk Enterprise ingesting Windows Security logs

Logs flow from the Windows endpoint into Splunk, where searches and scheduled detections are evaluated.

See screenshots in `/Screenshots/architecture/`.

Include 1 diagram or IP screenshot, not many.
