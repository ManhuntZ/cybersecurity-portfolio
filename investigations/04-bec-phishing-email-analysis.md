# Investigation 04 - BEC Phishing Email Analysis

**Date:** April 2026  
**Severity:** 🔴 Critical  
**MITRE ATT&CK:** T1566.001 - Phishing: Spearphishing Link, T1566 - Phishing (BEC)  
**Status:** Resolved  

## Summary
Investigated a Business Email Compromise (BEC) phishing email 
impersonating the company CEO requesting an urgent wire transfer. 
Email appeared to originate from the CEO's legitimate address but 
was sent from a malicious server. All replies would be redirected 
to the attacker's email address, enabling financial fraud and 
potential data theft.

## Tools Used
- MXToolbox Email Header Analyzer
- VirusTotal

## Investigation Process

### Step 1 - Email Header Analysis
Delivered-To: employee@company[.]com
Received: from mail[.]suspicious-server[.]ru
(mail[.]suspicious-server[.]ru [185[.]220[.]101[.]45])
by mx[.]company[.]com with ESMTP id x23si123456
for <employee@company[.]com>;
Mon, 15 Jan 2024 09:15:23 -0800
From: "CEO John Smith" <ceo@company[.]com>
Reply-To: attacker[@]gmail[.]com
To: employee@company[.]com
Subject: Urgent Wire Transfer Required
Message-ID: <abc123@suspicious-server[.]ru>

**Authentication Results (via mxtoolbox[.]com):**
- SPF: FAIL — 185[.]220[.]101[.]45 not authorized to send 
  for company[.]com
- DKIM: FAIL — signature verification failed
- DMARC: FAIL — policy set to reject

**Conclusion:** Email header confirms spoofed sender identity. 
All three authentication checks failed indicating the email 
did not originate from legitimate company infrastructure.

### Step 2 - Reply-To Manipulation Analysis
- **Display Name:** "CEO John Smith" (appears legitimate)
- **From Address:** ceo@company[.]com (spoofed)
- **Reply-To:** attacker[@]gmail[.]com (attacker controlled)

Victim sees CEO name and company email address but any reply 
goes directly to attacker. Classic BEC technique designed to 
intercept communication and redirect wire transfers.

### Step 3 - IP Analysis
- **IP:** 185[.]220[.]101[.]45
- **Location:** Germany
- **VirusTotal Score:** 16/91 malicious detections
- **Additional Activity:** IP linked to brute force attacks
- **Conclusion:** Confirmed malicious IP used for multiple 
  attack types indicating organized criminal infrastructure

## Key Findings
- BEC attack impersonating CEO to request fraudulent wire transfer
- Reply-To manipulation redirects all victim replies to attacker
- Email originated from German server despite Russian domain name
- Sending IP flagged for both phishing and brute force activity
- All SPF, DKIM, DMARC authentication checks failed
- Multi-vector attack infrastructure suggests organized operation

## IOCs (Indicators of Compromise)
- IP: 185[.]220[.]101[.]45
- Domain: mail[.]suspicious-server[.]ru
- Domain: suspicious-server[.]ru
- Email: attacker[@]gmail[.]com
- Subject: "Urgent Wire Transfer Required"

## Recommended Actions
1. Block IP 185[.]220[.]101[.]45 at firewall
2. Block domain suspicious-server[.]ru
3. Alert all employees about active BEC campaign
4. Implement strict DMARC policy (p=reject) for company domain
5. Train employees to verify wire transfer requests via phone
6. Submit 185[.]220[.]101[.]45 to VirusTotal community
7. Monitor for additional emails from same infrastructure

## Key Learnings
- Display name and From address can both be spoofed
- Reply-To field reveals true attacker destination
- SPF/DKIM/DMARC failures confirm email spoofing
- Server location and domain name can differ — always check IP
- Same infrastructure used for multiple attack types = 
  organized criminal operation
