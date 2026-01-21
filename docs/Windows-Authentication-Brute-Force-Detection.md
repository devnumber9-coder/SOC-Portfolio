# Windows Authentication Brute Force Detection (Splunk)

## Objective
Detect high-volume failed authentication attempts (brute force behavior) against a Windows host using Splunk by monitoring Windows Security logs (Event ID 4625) and generating an alert.

---

## Lab Environment
- Virtualization: VirtualBox  
- ttacker VM: Kali Linux  
- Target VM: Windows 11 Pro  
- SIEM: Splunk Enterprise (local web UI on the Windows host)  
- Log Source:Windows Security Event Log ingested into Splunk (`WinEventLog:Security`)

> Same environment as the Password Spray lab — this lab changes the attack pattern (many attempts) and the detection logic (rate/volume).

---

## Accounts Used (Target)
Local Windows accounts used for testing:
- `alice`
- `bob`
- `charlie`
- (existing local user) `SOC`

---

## Step 0 — Reset / Verify Ingestion
Goal: Confirm Splunk is ingesting Windows Security events before generating noise.

1. On Windows: confirm Security logs are present in Event Viewer  
   - `Event Viewer → Windows Logs → Security`

2. In Splunk: confirm events exist in the last hour/day
```spl
source="WinEventLog:Security"
```

**Screenshot(s) to capture**
- Splunk search showing Security events are returning (last 60 minutes or last 24 hours).

---

## Step 1 — Generate Brute Force-Like Activity
Goal: Create many failed logon events for one (or more) accounts to simulate brute force.

- From Kali, attempt repeated logins against the Windows host (RDP attempts) using the same username across many tries.
- Verify Windows logs show failures (4625) and/or credential validation failures (4776).

**What to validate in Event Viewer**
- Event ID 4625 = failed logon  
- Event ID 4776 = credential validation (NTLM) failures (often paired depending on the auth path)

**Screenshot(s) to capture**
- Event Viewer showing 4625 with:
  - Account name (e.g., `alice`)
  - Workstation name (e.g., `kali`) / Source Network Address (if present)
- (Optional) Event Viewer showing 4776 for the same timeframe.

---

## Step 2 — Baseline Splunk Search (4625 Volume by User)
- Goal: Prove Splunk sees the failed logons and identify which account is getting hit the most.

## A) Quick count by account field
```spl
source="WinEventLog:Security" EventCode=4625
| stats count by Account_Name
| sort - count
```

**Note on the `-` value**
- `-` typically means Splunk didn’t have that field populated for that specific event, or the account field was blank/NULL in that record.  
- This is normal when different Windows event formats populate different fields.

## B) Fix: normalize username into one field (recommended)
```spl
source="WinEventLog:Security" EventCode=4625
| eval user=coalesce(TargetUserName, Account_Name)
| stats count by user
| sort - count
```

**Screenshot(s) to capture**

---

## Step 3 — Time-Bucketed Detection (High-Volume Authentication Abuse)
Goal: Identify “bursts” of failures in a short time window (brute force pattern).

```spl
source="WinEventLog:Security" EventCode=4625
| eval user=coalesce(TargetUserName, Account_Name)
| bin _time span=1m
| stats count as failures by _time, user
| sort _time - failures
```

**How to interpret**
- Look for  high failures clustered in the same minute for the same user.

## Step 4 — Create the Alert (Brute Force Authentication Detection)
- Goal: Turn the detection search into a scheduled alert.

### Recommended alert search (add a threshold)
Pick a threshold that matches lab volume. Example: trigger when a user has **10+ failures in 1 minute**.

```spl
source="WinEventLog:Security" EventCode=4625
| eval user=coalesce(TargetUserName, Account_Name)
| bin _time span=1m
| stats count as failures by _time, user
| where failures >= 10
| sort _time - failures
```

### Scheduling
If wanted it to run every 5 minutes (cron):
- Cron schedule: `*/5 * * * *`

> In Splunk: Save As → Alert → Schedule → Cron Schedule and paste the expression above.

### Suggested alert settings
- Alert name: Brute Force Authentication Detection  
- Type:Scheduled  
- Severity: Critical  
- Trigger: “If results > 0”  
- Time range: last 5 minutes (or last 15 minutes for more coverage)


## Results / Evidence
Validated:
- Windows generated 4625 failures during the attack simulation.
- Splunk ingested those events (`WinEventLog:Security`) and could:
  - Count failures by user
  - Normalize username fields with `coalesce()`
  - Detect bursts using `bin _time span=1m`
- A scheduled alert triggered in Splunk.

---

1. Splunk ingestion baseline (Security events showing up)
2. Event Viewer 4625 showing attacked user + source workstation/address (if present)
3. Splunk: failures by user (coalesce query) showing attacked user high
4. Splunk: time-bucketed failures (`_time`, `user`, `failures`)
5. Triggered Alerts page showing the alert fired

---

## Notes / Troubleshooting 
- `TargetUserName` sometimes blank: use `coalesce(TargetUserName, Account_Name)` to normalize.
- `-` user bucket: represents missing/NULL username in those events (often background/system noise).
- Time range matters: use “Last 60 minutes” / “Last 4 hours” when testing to avoid mixing old lab data.


