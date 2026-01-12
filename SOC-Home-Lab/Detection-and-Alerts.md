## Detection Logic

A scheduled Splunk detection was created to identify potential brute-force authentication attempts.

### SPL Used
```spl
source="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count as failures by user, _time
| where failures >= 5
```

### Alert Behavior

The scheduled search executed successfully and returned matching events. However, no alert trigger occurred due to Windows account lockout behavior distributing failed attempts across multiple time buckets.

This behavior was confirmed via:
- Splunk search results
- Job Management execution logs

Detection logic was validated without relying on alert firing, reflecting real-world SOC practices.
