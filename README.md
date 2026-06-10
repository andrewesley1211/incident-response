<div align="center">

# 🚨 Incident Response
### Playbooks, Reports & Containment Workflows

[![Playbooks](https://img.shields.io/badge/Playbooks-5%20Active-red?style=for-the-badge)](#)
[![Framework](https://img.shields.io/badge/Framework-NIST%20SP%20800--61-003087?style=for-the-badge)](#)
[![Status](https://img.shields.io/badge/Status-Production%20Ready-2ea44f?style=for-the-badge)](#)

</div>

---

## 📋 Overview

This repository contains **incident response playbooks, post-incident reports, and containment workflows** built to NIST SP 800-61 Rev. 2 standards. Each playbook walks a SOC analyst through detection, analysis, containment, eradication, recovery, and post-incident review.

**Frameworks Applied:**
- NIST SP 800-61 Rev. 2 — Computer Security Incident Handling Guide
- MITRE ATT&CK — Adversary behavior mapping
- SANS Incident Response Process — 6-phase model

---

## 🗂️ Document Index

| Document | Type | Incident Category | Priority |
|---|---|---|---|
| [Phishing IR Playbook](./playbooks/phishing-playbook.md) | Playbook | Social Engineering | 🔴 P1 |
| [Ransomware IR Playbook](./playbooks/ransomware-playbook.md) | Playbook | Malware / Extortion | 🔴 P1 |
| [Unauthorized Access Playbook](./playbooks/unauthorized-access-playbook.md) | Playbook | Account Compromise | 🔴 P1 |
| [AWS S3 Exposure Playbook](./playbooks/aws-s3-exposure-playbook.md) | Playbook | Cloud Misconfiguration | 🟠 P2 |
| [DDoS Response Playbook](./playbooks/ddos-playbook.md) | Playbook | Availability Attack | 🟠 P2 |
| [Incident Report — PHI-2026-0312](./reports/incident-report-PHI-2026-0312.md) | Report | Phishing | Closed |
| [Incident Report — MAL-2026-0615](./reports/incident-report-MAL-2026-0615.md) | Report | Malware/RAT | Closed |

---

## 🔄 IR Process (NIST SP 800-61)

```
+-----------------------------------------------------------------+
|                    INCIDENT RESPONSE LIFECYCLE                  |
+----------+--------------+-------------+------------+-----------+
|  PREPARE  |    DETECT    |   CONTAIN   |  ERADICATE |  RECOVER  |
|           |   ANALYZE    |             |            |           |
| * Policy  | * SIEM alert | * Isolate   | * Remove   | * Restore |
| * Train   | * Triage     | * Preserve  | * Patch    | * Monitor |
| * Tools   | * Escalate   | * Block IOC | * Harden   | * Validate|
+----------+--------------+-------------+------------+-----------+
                                                          |
                                              POST-INCIDENT REVIEW
                                              * Lessons learned
                                              * Update playbooks
                                              * Report metrics
```

---

## 📂 Repository Structure

```
incident-response/
├── README.md
├── playbooks/
│   ├── phishing-playbook.md
│   ├── ransomware-playbook.md
│   ├── unauthorized-access-playbook.md
│   ├── aws-s3-exposure-playbook.md
│   └── ddos-playbook.md
├── reports/
│   ├── incident-report-PHI-2026-0312.md
│   └── incident-report-MAL-2026-0615.md
└── templates/
    └── incident-report-template.md
```

---

## 🔗 Related Repositories

- 🔍 [SOC Analyst Labs](https://github.com/andrewesley1211/soc-analyst-labs) — Lab investigations feeding these playbooks
- ☁️ [Cloud Security Projects](https://github.com/andrewesley1211/cloud-security-aws) — AWS-specific IR procedures
- 📊 [NIST CSF Gap Analysis](https://github.com/andrewesley1211/nist-csf-gap-analysis) — Framework coverage map
