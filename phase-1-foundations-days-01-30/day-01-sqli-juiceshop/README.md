# Day 01: SQL Injection — OWASP Juice Shop

> **Status:** 🔲 Pending · **Category:** Web App / Red · **Refs:** OWASP A03:2021 – Injection · MITRE ATT&CK T1190 (Exploit Public-Facing Application)

## Objective

[What this lab demonstrates — finding and exploiting SQL injection in a deliberately vulnerable app, extracting data, and writing it up to report standard.]

## Environment Setup

- Host: [machine / VM details]
- Target: OWASP Juice Shop via Docker — `docker run --rm -p 3000:3000 bkimminich/juice-shop`
- Tools: [browser dev tools, Burp Suite Community, sqlmap]

## Vulnerability / Technique

- **OWASP:** A03:2021 – Injection
- **MITRE ATT&CK:** T1190 – Exploit Public-Facing Application
- [Short technical description of the vulnerability class.]

## Steps to Reproduce

1. [Step…]
2. [Step…]

> Screenshots go in [`./screenshots/`](./screenshots/) — numbered and captioned.

## Root Cause Analysis

[Why the flaw exists — e.g. unparameterized SQL, string-concatenated queries — and what design decisions enabled it.]

## Remediation

[How a real deployment would fix it: parameterized queries/prepared statements, input validation, least-privilege database account, WAF, safe error handling.]

## What I Learned

[Key takeaways, surprises, follow-up questions.]
