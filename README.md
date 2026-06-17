# 📋 Incident Response Playbook — v1.0

[📄 View Full Playbook PDF](./IR_Playbook.pdf)

A SOC-ready incident response playbook covering three high-frequency threat categories: Phishing, Malware Infection, and Brute Force. Built on NIST SP 800-61r2 and structured for Tier 1, Tier 2, and IR teams.

> **Authors:** Omar Sherif & Radwa Khaled  
> **Framework:** NIST SP 800-61 Revision 2  
> **Version:** 1.0 — April 2026  
> **Applies To:** SOC Tier 1, Tier 2, IR Team, IT Administration

---

## What This Is

Most IR documentation either stays too high-level to be useful during an incident, or gets so detailed it becomes a manual no one reads under pressure. This playbook tries to sit in the middle: specific enough that a Tier 1 analyst can open it mid-alert and know exactly what to do, structured enough that nothing critical gets missed.

Each of the three playbooks follows the same 16-section structure, so analysts familiar with one can navigate any of the others without relearning the format.

---

## Playbooks

### Playbook 01 — Phishing Attack
**Threat category:** Social Engineering / Initial Access

Covers email-based attacks targeting credentials, malware delivery, and business email compromise. Includes SPF/DKIM/DMARC header analysis, safe URL inspection workflow, SIEM queries to find every recipient and every user who clicked, and containment steps for M365 environments.

Key scenario: employee receives a fake Microsoft 365 security alert, clicks the link, and submits credentials to a harvesting page.

### Playbook 02 — Malware Infection
**Threat category:** Endpoint Compromise / Execution & Persistence

Covers all malware families (RAT, ransomware, trojan, worm, spyware) on Windows, Linux, and macOS endpoints. Includes a full Sysmon + Splunk timeline query workflow, C2 beaconing detection in Wireshark, lateral movement checks via EventID 4624/LogonType=3, and a complete persistence location audit checklist.

Key scenario: employee runs a fake invoice.exe that installs Remcos RAT and establishes C2 on TCP/4782.

### Playbook 03 — Brute Force Attack
**Threat category:** Credential Access / Initial Access

Covers four attack patterns — classic brute force, password spray, credential stuffing, and distributed attacks — against VPN portals, RDP, SSH, Active Directory, and web login pages. Includes EventID correlation queries to confirm whether an attack succeeded, and post-access checks for backdoor accounts (EventID 4720) and privilege escalation (EventID 4672).

Key scenario: attacker runs credential stuffing against the company VPN using leaked breach data. Successful login at 03:47 UTC, backdoor account created at 03:52 UTC.

---

## Structure

Every playbook contains 16 standardized sections:

| # | Section | What It Contains |
|---|---------|-----------------|
| 1 | Objective | The response goal for this specific threat |
| 2 | Scope | What's in scope, what's excluded, who it applies to |
| 3 | Trigger Conditions | Specific alerts and events that activate the playbook |
| 4 | Detection & Identification | SIEM queries, Event IDs, and tool workflows |
| 5 | Analysis | Step-by-step investigation questions and methods |
| 6 | Severity Classification | Critical / High / Medium / Low with response time targets |
| 7 | Containment | Short-term (≤30 min) and long-term (24–48 hr) actions |
| 8 | Eradication | Full removal of the threat and its persistence mechanisms |
| 9 | Recovery | Safe restoration of affected systems and accounts |
| 10 | IOC Table | Realistic indicators of compromise for the scenario |
| 11 | Tools Used | Each tool with its specific role in this playbook |
| 12 | MITRE ATT&CK Mapping | Technique IDs, tactics, and context |
| 13 | Communication & Escalation | Who to notify, when to escalate, which channel |
| 14 | Documentation & Reporting | Minimum required fields in the incident ticket |
| 15 | Lessons Learned | Post-incident review questions |
| 16 | Automation (Bonus) | SOAR and API automation opportunities |

---

## SIEM Queries Included

All queries are written for **Splunk** with equivalent logic for **Wazuh**:

**Phishing**
- Find all recipients of a phishing email by sender domain
- Find every user who clicked the phishing URL (proxy logs)

**Malware**
- Full activity timeline of an infected host (Sysmon EventIDs 1, 3, 11, 13)
- Lateral movement check via EventID 4624 LogonType=3
- C2 beaconing detection (Wireshark filter included)

**Brute Force**
- Top attacking IPs by failure count (EventID 4625)
- Credential stuffing detection (1 IP, many accounts)
- Confirm if attack succeeded (4625 → 4624 sequence)

---

## MITRE ATT&CK Coverage

18 techniques mapped across all three playbooks:

| Threat | Tactic | Technique | ID |
|--------|--------|-----------|-----|
| Phishing | Initial Access | Phishing | T1566 |
| Phishing | Initial Access | Spearphishing Attachment | T1566.001 |
| Phishing | Initial Access | Spearphishing Link | T1566.002 |
| Phishing | Credential Access | Steal Web Session Cookie | T1539 |
| Phishing | Defense Evasion | Impersonation | T1656 |
| Phishing | Collection | Email Forwarding Rule | T1114.003 |
| Malware | Execution | User Execution: Malicious File | T1204.002 |
| Malware | Persistence | Registry Run Keys | T1547.001 |
| Malware | Defense Evasion | Masquerading | T1036 |
| Malware | C2 | Application Layer Protocol | T1071 |
| Malware | C2 | Non-Standard Port | T1571 |
| Malware | Exfiltration | Exfiltration Over C2 Channel | T1041 |
| Brute Force | Credential Access | Brute Force | T1110 |
| Brute Force | Credential Access | Password Spraying | T1110.003 |
| Brute Force | Credential Access | Credential Stuffing | T1110.004 |
| Brute Force | Initial Access | Valid Accounts | T1078 |
| Brute Force | Persistence | Create Account | T1136 |
| Brute Force | Discovery | Account Discovery | T1087 |

Full technique details at [attack.mitre.org](https://attack.mitre.org)

---

## Tools Referenced

| Tool | Used For |
|------|---------|
| Wazuh / Splunk | SIEM detection queries, event correlation, incident timeline |
| CrowdStrike / Microsoft Defender EDR | Process tree, network containment, file event timeline |
| VirusTotal | Hash, IP, and domain reputation across 70+ AV engines |
| ANY.RUN / Hybrid Analysis | Dynamic behavioral sandbox for safe malware execution |
| URLScan.io | Safe visual inspection of phishing pages |
| MXToolbox | Email header parsing, SPF/DKIM/DMARC verification |
| Wireshark | Packet capture, C2 beaconing analysis |
| Autoruns (Sysinternals) | Enumerate all autostart and persistence locations |
| AbuseIPDB | Attacking IP reputation and historical abuse reports |
| Shodan | Attacker IP infrastructure investigation |
| AlienVault OTX | Threat intelligence enrichment and IOC correlation |
| HaveIBeenPwned | Check affected accounts against known breach databases |
| Fail2Ban | Linux auto-ban on configurable failure threshold |
| Microsoft 365 Defender | Email quarantine, content search, session token revocation |

---

## Methodology

Each playbook was developed using:

- **Primary framework:** NIST SP 800-61 Revision 2 — Computer Security Incident Handling Guide
- **Threat intelligence:** MITRE ATT&CK Framework (attack.mitre.org)
- **Detection logic:** Wazuh SIEM documentation, Splunk Security Essentials, Sysmon Event ID reference
- **Tool references:** Official documentation for VirusTotal, ANY.RUN, CrowdStrike, Wireshark, AbuseIPDB

---

## What I Learned

- Writing detection logic that works at different severity levels — not just "alert fires → do everything"
- The difference between containment and eradication in practice, and why rushing to eradication before containment is complete is how incidents get worse
- Why out-of-band escalation paths matter — if the mail server is compromised, you can't rely on email to coordinate the response
- How SOAR automation maps to each phase of the NIST lifecycle, and which actions are safe to automate vs. which need analyst judgment

---

## Authors

**Omar Sherif** — SOC Analyst  
**Radwa Khaled** — SOC Analyst  

Framework: NIST SP 800-61r2 | April 2026
