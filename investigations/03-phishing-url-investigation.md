# Investigation 03 - Phishing URL & Threat Intelligence

**Date:** April 2026  
**Severity:** 🔴 High  
**Status:** Ongoing monitoring

## Summary
Investigated suspicious URL infrastructure using threat 
intelligence tools. Discovered active phishing campaign 
targeting PayPal and Google credentials across multiple 
countries.

## Initial Indicator
- URL: `paypal-secure-login[.]com`
- First flagged: 2013

## Tools Used
- VirusTotal
- URLscan.io

## Investigation Process

### Step 1 - URL Analysis
- **URL:** paypal-secure-login[.]com
- **VirusTotal Score:** 13/95 malicious
- **Conclusion:** Confirmed phishing site

### Step 2 - IP Pivot
- **IP:** 99[.]83[.]176[.]46
- **Location:** United States
- **VirusTotal Score:** 1/94
- **Hosted Domains:** 200+
- **Conclusion:** Shared hosting used to hide malicious sites

### Step 3 - Infrastructure Discovery
Connected malicious domains found on same IP:
| Domain | Risk | Reason |
|--------|------|--------|
| buypaypal[.]net | 🔴 High | Impersonates PayPal |
| buypaypal[.]it | 🔴 High | Multi-country PayPal phishing |
| googlecloud[.]com[.]br | 🔴 High | Impersonates Google Cloud |
| citizenstatebanktx[.]net | 🟡 Medium | Suspicious bank domain |

### Step 4 - Fresh Phishing Site Found
- **Domain:** googlecloud[.]com[.]br
- **Created:** 2026-04-15 (7 days old)
- **VirusTotal Score:** 3/94
- **Risk:** Active undetected phishing site

## Key Findings
- Multi-country phishing campaign targeting PayPal users
- Fresh Google impersonation site with low detection rate
- Shared hosting infrastructure used across all malicious domains
- Human analysis caught what automated tools missed

## IOCs (Indicators of Compromise)
- IP: 99[.]83[.]176[.]46
- Domain: paypal-secure-login[.]com
- Domain: buypaypal[.]net
- Domain: buypaypal[.]it
- Domain: googlecloud[.]com[.]br

## Recommended Actions
1. Block all identified domains at firewall
2. Block IP 99[.]83[.]176[.]46
3. Alert users about active PayPal/Google phishing campaign
4. Submit googlecloud[.]com[.]br to VirusTotal community
5. Monitor for new domains on same IP infrastructure

## Key Learnings
- Always pivot from URL → IP → related domains
- Newly registered domains are high risk even with low detection
- Shared hosting hides malicious sites among legitimate ones
- Threat intelligence requires connecting multiple data points
