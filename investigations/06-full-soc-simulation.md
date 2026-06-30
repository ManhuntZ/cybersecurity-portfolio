# Investigation 06 - Full SOC Simulation: Multi-Incident Overnight Review

**Date:** January 20, 2026   
**Severity:** 🔴 Critical (Incident 1), 🟡 Medium (Incidents 2 & 3)   
**MITRE ATT&CK:** T1110.001 - Brute Force: Password Guessing, 
T1560 - Archive Collected Data, T1567 - Exfiltration Over Web 
Service, T1566 - Phishing   
**Status:** Resolved (Incident 1), Monitoring (Incidents 2 & 3)   

## Summary
Overnight log review identified three distinct security events 
across multiple hosts. One confirmed critical breach involving 
successful brute force, data staging, and exfiltration of finance 
data. One failed brute force attempt against a separate workstation. 
One suspicious DNS query to a newly-patterned domain requiring 
further monitoring. Legitimate backup service activity was also 
present and used as a baseline to distinguish malicious behavior 
from normal operations.

## Tools Used
- Elastic Stack (Kibana)
- Kibana Query Language (KQL)

---

## Incident 1 - 🔴 CRITICAL: Brute Force → Data Exfiltration

### Timeline
| Time | Event |
|------|-------|
| 02:00:01 | Brute force begins against backup_svc from external IP 203[.]0[.]113[.]77 |
| 02:00:07 | 4th failed attempt |
| 02:00:09 | Successful login |
| 02:00:10 | Session opened |
| 02:00:40 | Attacker archives /data/finance into /tmp/archive.tar.gz |
| 02:01:15 | Archive uploaded via curl to external domain file-share-pro[.]net |
| 02:01:20 | Session closed |

### Investigation Process
Filtered for failed/accepted logins on fileserver01 — confirmed 
4 failed attempts in 8 seconds (automated tooling) followed by 
success from external IP 203[.]0[.]113[.]77. Cross-referenced 
sudo command logs immediately after login: attacker staged 
finance data into /tmp/ (not the legitimate /backup/ path used 
by scheduled backup jobs) and exfiltrated it to an unverified 
external domain within 75 seconds of gaining access.

### Baseline Comparison
Legitimate backup_svc sessions (08:01, 14:00) originate from 
internal IP 10[.]0[.]0[.]50 and write archives to /backup/ — 
never /tmp/, and never followed by an outbound upload command. 
This contrast confirms Incident 1 as malicious, not a false 
positive against normal backup activity.

### Conclusion
Confirmed breach with active data exfiltration targeting financial 
data specifically — not opportunistic, but deliberate targeting.

---

## Incident 2 - 🟡 MEDIUM: Failed Brute Force (No Breach)

### Timeline
| Time | Event |
|------|-------|
| 09:14:22 | Failed login attempt against "admin" from 198[.]51[.]100[.]23 |
| 09:14:26 | 3rd and final failed attempt — no further activity |

### Conclusion
Automated probing attempt against workstation12. No successful 
login, no follow-up activity from this IP afterward. Lower 
severity than Incident 1 since no breach occurred, but still 
requires monitoring as reconnaissance for a future attempt.

---

## Incident 3 - 🟡 MEDIUM: Suspicious DNS Query (Unconfirmed)

### Timeline
| Time | Event |
|------|-------|
| 07:02:11 | workstation07 (user j.lee) queries mail-update-secure[.]com, resolves to 91[.]243[.]55[.]12 |

### Conclusion
Domain name follows known phishing/typosquat naming pattern — 
combines a trusted-sounding word ("mail," "secure," "update") 
with a hyphenated, non-corporate domain, matching the same 
pattern identified in Investigation 03 (paypal-secure-login[.]com, 
buypaypal[.]net). No confirmed malicious action beyond the DNS 
query itself. Requires IP/domain reputation check via VirusTotal 
and user follow-up before closing.

---

## Detection Playbook Rule (New)
**Trigger:** Alert when an SSH session from an external IP is 
immediately followed by an archive command (tar/zip) writing to 
/tmp/ (or any non-standard backup path), followed by an outbound 
transfer command (curl/wget/scp) to a non-whitelisted external 
domain — all within a short time window (under 5 minutes).
This isolates true exfiltration behavior while excluding 
legitimate scheduled backups that write to /backup/ and never 
initiate outbound transfers.

## IOCs (Indicators of Compromise)
- IP: 203[.]0[.]113[.]77 (brute force + exfiltration source)
- IP: 198[.]51[.]100[.]23 (failed brute force source)
- IP: 91[.]243[.]55[.]12 (resolved IP for suspicious domain)
- Domain: file-share-pro[.]net (exfiltration destination)
- Domain: mail-update-secure[.]com (suspicious, unconfirmed)
- Account: backup_svc (compromised in Incident 1)

## Recommended Actions

**Incident 1 (Critical):**
1. Disable backup_svc account immediately, rotate credentials
2. Block IP 203[.]0[.]113[.]77 at firewall
3. Block domain file-share-pro[.]net
4. Notify management — confirmed finance data exfiltration
5. Determine legal/compliance reporting obligations (data breach)
6. Audit all backup_svc activity for prior undetected access

**Incident 2 (Medium):**
1. Block IP 198[.]51[.]100[.]23 at firewall
2. Monitor workstation12 for renewed attempts
3. No further action required unless activity resumes

**Incident 3 (Medium):**
1. Submit mail-update-secure[.]com and 91[.]243[.]55[.]12 to VirusTotal
2. Contact j.lee to confirm if domain was visited intentionally 
   (e.g., clicked link) or auto-loaded
3. Block domain pending investigation results
4. Escalate to Critical if user confirms credential entry or 
   attachment download

## Key Learnings
- Severity must be ranked by impact and completion of the attack, 
  not just by attack type — a successful breach with exfiltration 
  outranks a failed attempt against a different host even though 
  both are "brute force"
- Comparing malicious activity against a known-legitimate baseline 
  (the scheduled backup_svc sessions) is what proves an event is 
  an anomaly rather than normal operations
- Detection rules should target the deviation from normal behavior 
  (/tmp/ + external upload), not surface-level similarity (any 
  tar command), or they generate false positives and alert fatigue
- A suspicious DNS query alone is not a confirmed incident — it's 
  a lead requiring follow-up, and should be triaged accordingly 
  rather than treated with the same urgency as a confirmed breach
