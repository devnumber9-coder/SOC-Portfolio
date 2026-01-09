# Logging

## Windows 11 Security Logging

These events support detection of brute-force attacks, unauthorized access attempts, and suspicious process activity.

Enabled local audit policies to collect authentication and process creation events logging.
- Audit logon events → Success & Failure 
- Audit account logon events → Success & Failure
- Audit account management → Success & Failure

Key events collected:
- Event ID :4624 Failed logon attempts
- Event ID 4624: Successful logons attempts
- Event ID 4688: Process creation with command-line arguments

## SIEM Configuration

_Add details about your SIEM setup and log aggregation._
