## Limitations and Findings

What Worked:
- Successfully ingested Windows Security logs into Splunk
- Detection logic correctly identified failed authentication attempts
- Dashboard search validated presence of Event ID 4625

What Didn't Work:
- Alert did not fire due to account lockout after 5 failed attempts
- Scheduled search threshold was never reached

Lessons Learned:
- Detection ≠ Alert triggering
- Endpoint behavior (lockout) impacts alert logic
- Dashboard-based validation is critical for testing detection rules