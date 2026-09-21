# Day 01: SQL Injection - OWASP Juice Shop

> **Status:** ✅ Completed · **Category:** Web App / Red · **Refs:** OWASP A03:2021 - Injection · MITRE ATT&CK T1190 (Exploit Public-Facing Application)

## Objective

Exploited a SQL Injection vulnerability in the OWASP Juice Shop login form to
bypass authentication and gain unauthorized access to an administrator
account, demonstrating OWASP A03:2021 (Injection).

## Environment Setup

- Host: Virtual Machine
- Target: OWASP Juice Shop via Docker - `docker run --rm -p 3000:3000 bkimminich/juice-shop`
- Tools: browser dev tools, docker

## Vulnerability / Technique

- **OWASP:** A03:2021 - Injection
- **MITRE ATT&CK:** T1190 - Exploit Public-Facing Application
- [Short technical description of the vulnerability class.]

## Steps to Reproduce

1. Navigated to http://localhost:3000/#/login
2. Entered the payload ' OR 1=1-- into the Email field
3. Submitted the login form with an arbitrary password
4. Captured the request in Firefox DevTools (Network tab), confirming the
   payload was sent unsanitized in the JSON body
5. Verified the server response, which returned a valid authentication
   token and umail: "admin@juice-sh.op" - confirming successful
   authentication bypass into the administrator account
> Screenshots go in [`./screenshots/`](./screenshots/) - numbered and captioned.

## Root Cause Analysis

The login form's backend likely constructs its SQL query using string
concatenation, e.g.:

    SELECT * FROM Users WHERE email = '<input>' AND password = '<input>'

The payload ' OR 1=1-- exploits this by injecting SQL syntax directly into
the query string:
- The single quote (') closes the intended email string early, breaking out
  of "data" context back into "SQL command" context.
- OR 1=1 introduces a condition that is always true, so the WHERE clause
  matches rows regardless of the actual email/password values.
- -- is a SQL comment marker, causing the database to ignore the rest of the
  query (including the password check).

The underlying root cause is that the application never separates user input
(data) from the SQL command (code) - it trusts that whatever is typed into
the email field will behave as plain text, rather than validating or
escaping it before use. This lack of separation is what allows injected SQL
syntax to be executed as part of the query logic instead of being treated
as a literal string.

## Remediation

- **Use parameterized queries / prepared statements.** Instead of building
  the SQL string by concatenation, the query and the user input are sent to
  the database separately:

      SELECT * FROM Users WHERE email = ? AND password = ?

  The database engine treats `?` placeholders strictly as data values, never
  as executable SQL - so a payload like ' OR 1=1-- would just be searched
  for literally as a bizarre email address, and simply not match any user.

- **Apply input validation** as defense-in-depth (e.g. reject emails that
  don't match a valid email format) - not a full fix on its own, but reduces
  attack surface.

- **Enforce least-privilege database accounts**, so even if injection
  succeeded, the application's DB user couldn't read/modify data outside
  what it strictly needs.

- **Add rate-limiting and account lockout** on repeated failed logins,
  limiting brute-force and injection-probing attempts.

- **Use safe, generic error handling** - never return raw database error
  messages to the client, since attackers use those to fingerprint the
  database type and refine injection payloads.

## What I Learned

[Key takeaways, surprises, follow-up questions.]
