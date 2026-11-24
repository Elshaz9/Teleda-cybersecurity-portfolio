# TELEDA IT & Cybersecurity Portfolio

Welcome to my hands-on IT Support and Cybersecurity portfolio. This repository documents my practical lab work, security investigations, system administration exercises, monitoring activities, and lessons I learned as I develop my skills toward IT Support and Tier 1 Security Operations Center (SOC) roles.

## About this portfolio

I am using a virtual lab to practice an enterprise-style administration and security monitoring. My work includes Windows and Linux systems, Active Directory, Group Policy, Wazuh, and Suricata.

The focus is on documenting the analyst workflow: *alert or task → validation → investigation → evidence → findings → response or recommendation*.

## Project Portfolio

# Featured SOC investigations

| ID | Investigation | Technology | Status |
|---|---|---|---|
| SOC-001 | Possible TCP SYN port scan | Suricata, EveBox, Kali Linux | Completed |
| SOC-002 | Failed SSH login activity | Linux authentication logs, Wazuh | Completed |
| SOC-003 | Windows file access auditing | Windows Security Event 4663, Wazuh | Investigation documented; portfolio writeup to be added |

Each case study will include its objective, environment summary, steps performed, relevant event or rule identifiers, investigation findings, evidence references, and lessons learned.

## Repository map

- **[SOC Investigations](01-SOC-Investigations/README.md)** — investigation reports and alert analysis.
- **[Active Directory](02-Active-Directory/README.md)** — domain administration, users and groups, Group Policy, and auditing.
- **[Security Monitoring](03-Security-Monitoring/README.md)** — Wazuh and Suricata configuration and monitoring notes.
- **[IT Support](04-IT-Support/README.md)** — troubleshooting and systems support exercises.
- **[Scripts and Queries](05-Scripts-and-Queries/README.md)** — reviewed PowerShell, Linux commands, and SIEM queries.
- **[Evidence Guide](06-Evidence/README.md)** — index and handling guidance for sanitized screenshots and supporting evidence.

# Lab technologies

- Windows Server and Windows 11
- Active Directory Domain Services and Group Policy on windows server
- Ubuntu Linux and Kali Linux
- Wazuh SIEM / XDR
- Suricata IDS | EveBox for gui
- VirtualBox
- PowerShell, Linux shell, and Wazuh Discover / DQL

# How I document work

For each project, I aim to record:

1. Objective — what I set out to validate, investigate, or configure.
2. Environment — relevant systems and tools, with sensitive details generalized.
3. Procedure — the steps and commands used, with important parameters explained.
4. Evidence — relevant event records, screenshots, and query results.
5. Analysis — what the evidence shows and what it does not prove.
6. Outcome — actions taken, result, and any follow-up work.
7. Lessons learned — practical takeaways and improvements.


## Current focus

- Improving Windows event investigation and Wazuh query skills.
- Documenting Active Directory security configuration and Group Policy validation.
- Building clear, evidence-led SOC investigation reports.
- Organizing reusable commands and queries with explanations.

Thank you for visiting my portfolio.
