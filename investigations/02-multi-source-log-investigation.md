# Investigation 02 - Multi-Source Log Investigation

**Date:** January 15, 2026  
**Severity:** 🔴 Critical (one breach), 🟡 Medium (failed attempts)
**MITRE ATT&CK:** T1110.001 - Brute Force: Password Guessing, T1110.004 - Brute Force: Credential Stuffing  
**Status:** Resolved

## Summary
Analyzed authentication logs from multiple sources. 
Identified four suspicious activities including one successful 
breach, two external probes, and one internal anomaly.

## Findings

### 1. 🔴 CRITICAL - Successful Brute Force
- **IP:** 192[.]241[.]220[.]54 (External)
- **Target:** User "pi"
- **Activity:** 10 failed attempts over 10 seconds
- **Result:** Successful login at 11:00:11
- **Conclusion:** Server compromised

### 2. 🟡 MEDIUM - External Probe
- **IP:** 45[.]33[.]32[.]156 (External)
- **Activity:** Multiple failed attempts, invalid usernames
- **Result:** Unsuccessful
- **Conclusion:** Automated credential stuffing attack

### 3. 🟡 MEDIUM - External Probe
- **IP:** 67[.]21[.]45[.]89 (External)
- **Activity:** 3 failed attempts, invalid usernames
- **Result:** Unsuccessful
- **Conclusion:** Automated scanning attempt

### 4. 🟢 LOW - Internal Anomaly
- **IP:** 10[.]0[.]0[.]9 (Internal)
- **Activity:** 2 failed attempts, 1 successful login
- **Interval:** ~4 seconds between attempts
- **Conclusion:** Likely human error (typos), no suspicious 
post-login activity detected

## Tools Used
- Elastic Stack (Kibana)
- Kibana Query Language (KQL)

## Response Actions
1. Block IP 192[.]241[.]220[.]54 at firewall
2. Isolate compromised server immediately
3. Terminate active session for user "pi"
4. Investigate post-compromise activity on server
5. Monitor 45[.]33[.]32[.]156 and 67[.]21[.]45[.]89 for continued attempts
6. Verify 10[.]0[.]0[.]9 activity with employee

## Key Learnings
- External IPs trying invalid usernames = credential stuffing
- Internal IP with few attempts = likely human error
- Always investigate post-login activity after confirmed breach
- Different attacker behaviors require different response levels
