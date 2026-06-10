# 🎣 Incident Response Playbook — Phishing Attack

**Version:** 2.1  
**Owner:** Security Operations Center  
**Framework:** NIST SP 800-61 Rev. 2  
**Last Updated:** June 2026  
**Review Cycle:** Quarterly  

---

## 🎯 Scope & Purpose

This playbook provides a structured response to phishing email incidents — including credential harvesting campaigns, malware delivery via email, and business email compromise (BEC). It applies when a phishing email has been reported, identified in quarantine, or when downstream indicators suggest a user may have been compromised.

---

## Severity Classification

| Severity | Trigger Condition |
|---|---|
| P1 — Critical | User clicked link AND entered credentials OR opened malicious attachment |
| P2 — High | User clicked link but did not interact further |
| P3 — Medium | Phishing email received and reported — no user interaction |
| P4 — Low | Bulk campaign identified via threat intel — no company targeting |

---

## Phase 1: DETECTION & TRIAGE (0-15 min)

**Trigger Sources:**
- Employee report via email/helpdesk ticket
- Email gateway quarantine alert
- SIEM alert on suspicious URL click
- EDR behavioral alert on file execution

**Triage Checklist:**

- [ ] Assign incident ticket in ServiceNow
- [ ] Identify the reporting user(s) — name, department, device
- [ ] Retrieve the raw email (headers + body + attachments) from mail gateway
- [ ] Confirm classification: phishing vs. spam vs. legitimate marketing
- [ ] Check if link was clicked (email gateway click-tracking logs)
- [ ] Check if attachment was opened (EDR telemetry)
- [ ] Determine if credentials were entered (check with user)
- [ ] Search for other recipients of same email across the organization

**Triage Decision Tree:**

```
Email Reported
     |
     v
Did user click the link?
  +- NO -> Monitor + Block IOCs -> P3
  +- YES -> Did user enter credentials?
              +- NO -> Did user open attachment?
              |         +- NO -> P2
              |         +- YES -> P1 (escalate immediately)
              +- YES -> P1 (escalate immediately)
```

---

## Phase 2: ANALYSIS (15-60 min)

**Email Analysis Steps:**

```bash
# 1. Extract and analyze email headers
#    Tools: MXToolbox Header Analyzer, PhishTool, Google Admin Toolbox
# Key header checks:
# - SPF / DKIM / DMARC results
# - Return-Path vs. From address mismatch
# - Reply-To pointing to different domain
# - Originating IP geolocation

# 2. URL Analysis
#    Tools: VirusTotal, URLScan.io, AbuseIPDB

# 3. Attachment Analysis (NEVER open on production machine)
#    Tools: Any.run, Hybrid Analysis, VirusTotal

# 4. Check IOCs against threat intel feeds
#    Tools: AlienVault OTX, MISP, Recorded Future
```

**Splunk Queries for Scope Assessment:**

```splunk
# Find all users who received the same email
index=email_logs subject="[URGENT] Your Microsoft 365 account*"
| table _time, recipient, sender, subject, attachment_name

# Find all click events for the phishing URL
index=proxy_logs url="*micros0ft-helpdesk.com*"
| table _time, src_ip, user, url, action

# Check for authentication events from affected users
index=windows_events EventCode=4624 Account_Name="affected.user"
| table _time, Account_Name, src_ip, Logon_Type
| sort -_time
```

---

## Phase 3: CONTAINMENT (30-90 min)

**Immediate Actions:**

- [ ] Block sender domain at email gateway
- [ ] Quarantine all copies of phishing email from all mailboxes
- [ ] Block phishing URL at web proxy / DNS filter
- [ ] Block attacker IP at perimeter firewall
- [ ] Isolate affected endpoint from network if attachment was executed
- [ ] Preserve evidence — capture memory dump if malware executed

**Credential Compromise (P1 only):**

- [ ] Force immediate password reset for affected user(s)
- [ ] Revoke all active SSO / OAuth tokens for affected accounts
- [ ] Enable step-up MFA challenge for affected accounts
- [ ] Check for mail forwarding rules created by attacker
- [ ] Review OAuth app authorizations for the affected account
- [ ] Notify IT Identity team and HR if executive-level account affected

**Commands:**

```powershell
# Block domain at email gateway (Exchange Online PowerShell)
New-TenantAllowBlockListItems -ListType Domain -Block `
  -Entries "micros0ft-helpdesk.com","suspiciousdomain.xyz" -NoExpiration

# Force password reset
Set-MsolUserPassword -UserPrincipalName user@company.com -ForceChangePassword $true
```

---

## Phase 4: ERADICATION (1-4 hours)

- [ ] Confirm all phishing emails removed from all mailboxes
- [ ] Confirm all IOCs blocked (test from isolated device)
- [ ] Validate no persistence mechanisms installed (if malware executed)
- [ ] Review mail gateway rules — ensure autoforward rules not created by attacker
- [ ] Reset affected user credentials and re-enroll MFA device

---

## Phase 5: RECOVERY (4-24 hours)

- [ ] Restore email service for affected users
- [ ] Re-image endpoint if malware execution confirmed
- [ ] Monitor affected accounts for 48 hours post-recovery
- [ ] Verify no anomalous logins or data access in SIEM
- [ ] Send all-clear notification to affected users and management

---

## Phase 6: POST-INCIDENT REVIEW (48-72 hours after closure)

**Required Deliverables:**

1. **Incident Report** — Use `incident-report-template.md`
2. **Lessons Learned** — What gaps enabled the attack?
3. **Playbook Update** — Update this document if new TTPs observed
4. **Metrics Capture:**
   - MTTD (Mean Time to Detect): _____ minutes
   - MTTR (Mean Time to Respond): _____ hours
   - Users impacted: _____
   - Data at risk: Yes / No

**Common Improvement Actions:**

| Gap Identified | Recommended Control |
|---|---|
| DMARC not enforced | Enable p=reject on all owned domains |
| Users clicking links | Phishing simulation training (KnowBe4, Proofpoint) |
| Slow detection | Tune SIEM alert threshold for click-tracking |
| No URL sandboxing | Enable Safe Links (M365 Defender) or Zscaler |

---

## References

- NIST SP 800-61 Rev. 2 — Computer Security Incident Handling Guide
- MITRE T1566 — Phishing
- Microsoft — Investigating Phishing Attacks in M365
- CISA Phishing Guidance
