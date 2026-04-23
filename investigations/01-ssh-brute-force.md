# Investigation 01 - SSH Brute Force Attack

**Date:** January 15, 2026
**Severity:** 🔴 Critical
**Status:** Resolved

## Summary
Detected successful SSH brute force attack against root account.
Attacker gained full system access after 6 automated attempts.

## Timeline
| Time | Event |
|------|-------|
| 09:23:11 | Brute force attack begins from 192[.]168[.]1[.]105 |
| 09:23:16 | 6th failed attempt |
| 09:23:47 | Successful login - system compromised |
| 09:23:48 | Session opened for root |

## Tools Used
- Elastic Stack (Kibana)
- Kibana Query Language (KQL)

## Queries Used
source="attack.log" "Failed password"
source="attack.log" "Accepted password"
source="attack.log" "192[.]168[.]1[.]105"

## Findings
- Automated bot performing 1 attempt per second
- 6 failed attempts before success
- Full root access achieved
- Single source IP: 192[.]168[.]1[.]105

## Response Actions
1. Block IP 192[.]168[.]1[.]105 at firewall
2. Terminate active session
3. Change root password immediately
4. Restrict SSH access to known IPs only
5. Investigate post-compromise activity

## Lessons Learned
- SSH port 22 should be changed to non-standard port
- Implement fail2ban to automatically block brute force attempts
- Disable root SSH login entirely
- Use SSH keys instead of passwords
