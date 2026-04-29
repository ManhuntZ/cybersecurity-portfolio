# Investigation 05 - Windows Event Log Investigation

**Date:** April 2026  
**Severity:** 🔴 Critical  
**MITRE ATT&CK:** T1110 - Brute Force, T1059.003 - Windows Command Shell, T1204 - Malicious File,  
T1136 - Create Account, 
T1078 - Valid Accounts, T1070.001 - Clear Windows Event Logs  
**Status:** Resolved  

## Summary
Investigated Windows Security Event Logs revealing a complete 
attack chain against SERVER01. Attacker conducted brute force 
attack against administrator account, gained access, deployed 
malware, created a backdoor account with administrator privileges, 
and attempted to cover tracks by clearing audit logs. Attack 
went undetected for approximately 2 hours before log clearing 
event triggered investigation.

## Tools Used
- Elastic Stack (Kibana)
- Kibana Query Language (KQL)
- Windows Event Viewer

## Attack Timeline

| Time | Event ID | Description |
|------|----------|-------------|
| 09:00:01 | 4625 | Brute force attack begins from 45[.]33[.]32[.]156 |
| 09:00:19 | 4625 | 10th failed login attempt |
| 09:00:21 | 4624 | Successful login as administrator |
| 09:00:22 | 4688 | CMD launched by administrator |
| 09:00:25 | 4688 | Malicious process svch0st[.]exe deployed |
| 09:00:30 | 4720 | New account created: backdoor.user |
| 09:00:35 | 4732 | backdoor.user added to Administrators group |
| 11:00:00 | 1102 | Audit log cleared — attacker covering tracks |

## Investigation Process

### Step 1 - Login Analysis
Filtered Security logs for Event ID 4625 (failed logins):
- 10 failed attempts against administrator account
- All from external IP 45[.]33[.]32[.]156
- Attempts every 2 seconds indicating automated tooling
- Followed immediately by successful login (Event ID 4624)
- Conclusion: Successful brute force attack confirmed

### Step 2 - Post-Compromise Activity
Filtered for Event ID 4688 (process creation):
- CMD launched immediately after login — attacker taking control
- svch0st[.]exe launched from C:\Users\Public\temp\ 
- Filename masquerades as legitimate svchost.exe
- Location in Public\temp\ folder is highly suspicious
- Conclusion: Malware deployed for persistence

### Step 3 - Persistence Mechanism
Filtered for Event ID 4720 and 4732:
- New account "backdoor.user" created at 09:00:30
- Immediately added to Administrators group at 09:00:35
- Attacker created permanent access even if original 
  compromise is discovered
- Conclusion: Backdoor account established for persistent access

### Step 4 - Anti-Forensics
Filtered for Event ID 1102:
- Audit log cleared at 11:00:00
- Approximately 2 hours after initial compromise
- Attacker attempted to destroy evidence
- Conclusion: Failed — logs already forwarded to SIEM

## Key Findings
- Complete attack chain from brute force to persistence
- Malware filename masquerades as legitimate Windows process
- Backdoor account created with full administrator privileges
- 2 hour dwell time before attacker cleared logs
- Log clearing failed — SIEM already captured all events
- Same external IP (45[.]33[.]32[.]156) seen in previous investigations

## IOCs (Indicators of Compromise)
- IP: 45[.]33[.]32[.]156
- Account: backdoor.user
- Process: svch0st[.]exe
- Path: C:\Users\Public\temp\svch0st[.]exe

## Recommended Actions
1. Isolate SERVER01 from network immediately
2. Block IP 45[.]33[.]32[.]156 at firewall
3. Disable and delete backdoor.user account
4. Remove svch0st[.]exe and investigate persistence mechanisms
5. Reset administrator account password
6. Scan all systems for svch0st[.]exe presence
7. Check other servers for lateral movement from SERVER01
8. Review 2 hour dwell time for data exfiltration evidence

## Key Learnings
- Attackers clear logs to hide tracks — but SIEM captures 
  everything in real time
- Malware uses filename masquerading to blend with 
  legitimate processes
- Backdoor accounts provide persistent access after 
  initial compromise
- Dwell time allows silent data collection before detection
- Same IP appearing across multiple investigations suggests 
  organized attacker
- Log clearing event (1102) itself is evidence of compromise
