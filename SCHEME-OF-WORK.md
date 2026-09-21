# 📋 Scheme of Work — 90 Days of Cybersecurity

> The full curriculum behind this program. The [README](./README.md) is the daily-facing index; this document is the plan.

**Learner:** Nebeuwa Ifeanyichukwu Raphael ([@IfyTechy](https://github.com/IfyTechy))
**Track:** Blue Team (SOC Analyst) Foundations → Offensive (Red Team) Depth → Cloud Security Specialization
**Duration:** 90 days · 3 phases of 30 days · one hands-on project every day
**Certification path:** CompTIA Security+ (Day ~45–50) → eJPT (Day ~80–85) → OSCP (post–Day 90)

## Aims

1. Build a defensible, evidence-backed portfolio — every day produces a real lab write-up, not a checklist tick.
2. Cover the core skill areas of a junior SOC / security analyst role: web exploitation, networking, hardening, AD, cloud, SIEM, DFIR, malware analysis.
3. Prove depth through three flagship deliverables: a mini pentest report (Day 30), an attack + detect + respond case study (Day 60), and a capstone project (Days 87–90).

## Core Learning Environment

| Component | Purpose |
|---|---|
| Kali Linux (VM) | Attacker workstation — offensive tooling |
| VirtualBox / VMware VMs | Vulnerable targets, AD lab, Windows hosts |
| Docker | Disposable vulnerable apps (OWASP Juice Shop, DVWA) |
| AWS / Azure free tier | Cloud security labs (IAM, S3, Entra ID) |

## Documentation Standard

Every day produces a `day-XX-topic/README.md` following: **Objective → Environment Setup → Vulnerability/Technique (OWASP / MITRE ATT&CK) → Steps to Reproduce → Root Cause Analysis → Remediation → What I Learned.**

---

## Phase 1 — Foundations in Practice (Days 1–30)

Goal: command the fundamentals of web exploitation, network security, and OS hardening — then prove them in a real mini pentest.

| Days | Block | Category | Key Tools & Targets | Deliverable |
|---|---|---|---|---|
| 01 | SQL Injection | Web App / Red | OWASP Juice Shop, Burp Suite, sqlmap | [Day 01 write-up](./phase-1-foundations-days-01-30/day-01-sqli-juiceshop/) |
| 02–05 | XSS · Broken Auth · IDOR · CSRF | Web App / Red | OWASP Juice Shop, Burp Suite | Daily write-ups |
| 06–10 | Nmap · Wireshark · Firewalls · MITM | Network Security | Nmap, Wireshark, pfSense, ARP spoofing | Daily write-ups |
| 11–15 | Linux Hardening & Auditing | Infra / Blue | Lynis, auditd, iptables/nftables, SSH hardening | Daily write-ups |
| 16–20 | Active Directory Basics & Windows Hardening | Infra / Blue | Windows Server VM, GPOs, Sysinternals | Daily write-ups |
| 21–25 | Python / Bash Security Scripting | Automation | Python 3, Bash, cron | Daily write-ups |
| 26–29 | Vulnerability Chaining | Web App / Red | Juice Shop / DVWA multi-step chains | Daily write-ups |
| **30** | **Milestone 1 — Mini Pentest Report** | **Flagship** | All Phase 1 skills | [Milestone report](./phase-1-foundations-days-01-30/milestone-01-mini-pentest-report/) |

**Milestone 1 exit criteria:** a standalone pentest report (scope, methodology, findings with evidence, severity ratings, remediation) covering at least three vulnerabilities found in the lab.

## Phase 2 — Offense & Defense Depth (Days 31–60)

Goal: go deeper on both sides — AD attacks, cloud misconfigurations, SIEM detection engineering, DFIR, malware analysis — then chain offense and defense into one case study.

| Days | Block | Category | Key Tools & Targets | Deliverable |
|---|---|---|---|---|
| 31–36 | Active Directory Attacks | Red Team | Kerberoasting, Impacket, BloodHound | Daily write-ups |
| 37–41 | Cloud Security Basics | Cloud | AWS IAM & S3, Azure AD (Entra ID) | Daily write-ups |
| 42–47 | SIEM & Detection Engineering | Blue Team | ELK stack, Wazuh | Daily write-ups |
| 48–52 | Digital Forensics & Incident Response | Blue / DFIR | Autopsy, Volatility, log analysis | Daily write-ups |
| 53–57 | Malware Analysis Basics | Blue / DFIR | Static & dynamic analysis, sandboxing | Daily write-ups |
| 58–59 | Buffer — Milestone 2 prep & Security+ study | — | — | Case-study scaffold |
| **60** | **Milestone 2 — Attack + Detect + Respond Case Study** | **Flagship** | Red + Blue combined | [Case study](./phase-2-offense-defense-days-31-60/milestone-02-attack-detect-respond/) |

**Milestone 2 exit criteria:** execute an attack in the lab, show the telemetry it produced, build a detection that fires on it, and run a structured response — one end-to-end narrative.

## Phase 3 — Specialization & Portfolio Polish (Days 61–90)

Goal: specialize (Blue Team + Cloud), prove skills on public platforms, convert the work into job-ready materials, and ship the capstone.

| Days | Block | Category | Key Tools & Targets | Deliverable |
|---|---|---|---|---|
| 61–75 | Blue Team + Cloud Security Deep Dive | Specialization | Focus-track labs | Daily write-ups |
| 76–82 | CTFs & Write-ups | Practice | TryHackMe, HackTheBox | Ranked write-ups |
| 83–86 | Resume + LinkedIn Overhaul | Career | — | Job-ready resume & profile |
| **87–90** | **Capstone Project** | **Flagship** | End-to-end | [Capstone](./phase-3-specialization-days-61-90/capstone-project/) |

**Capstone exit criteria:** a self-directed project combining at least two of attack, detection engineering, incident response, and cloud security — documented as a professional deliverable.

---

## Daily Rhythm

1. Execute the day's lab.
2. Document it the same day using the standard template.
3. Update the README status table and the [Skills Matrix](./SKILLS-MATRIX.md).
4. Push to GitHub and post a short progress note on LinkedIn.

## Review Cadence

- **Every 10 days:** self-review of the Skills Matrix; identify and note weak areas.
- **Day 30 / Day 60:** milestone review — confirm exit criteria are met before moving on.
- **Day 90:** program retrospective and final portfolio audit.
