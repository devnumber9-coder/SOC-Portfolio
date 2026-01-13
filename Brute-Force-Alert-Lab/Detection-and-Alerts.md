## Detection and Alerts

Detection Logic:
```spl
index=windows_security EventCode=4625
| stats count by Account_Name
| where count > 5
```

Alert Configuration:
- Trigger Condition: More than 5 failed logons for a single account
- Time Window: 5 minutes
- Alert Action: Email notification

Findings:
- Alert did not trigger due to Windows account lockout policy
- Detection logic was validated through dashboard searches
- Learned the difference between alerting vs detection validation