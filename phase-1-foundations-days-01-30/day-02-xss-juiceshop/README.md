# Day 2: Cross-Site Scripting (XSS) - Exploiting Juice Shop & Building a Vulnerable App From Scratch

## Objective
Exploited a DOM-based Cross-Site Scripting (XSS) vulnerability in OWASP
Juice Shop's search function to execute attacker-controlled JavaScript in
the browser, demonstrating OWASP A03:2021 (Injection). Extended the
exercise by building a custom search feature from scratch, deliberately
introducing the same vulnerability class, then patching it - to understand
the exact code-level mechanism that causes and prevents XSS, rather than
just reproducing a known payload.

## Environment Setup
- OWASP Juice Shop, deployed via Docker (container reused from Day 1)
- Verified running via `docker ps`; restarted with `docker start juice-shop`
  after the container had stopped between sessions
- Accessed at http://localhost:3000
- Custom proof-of-concept built in plain HTML/JavaScript, served locally
  with the VS Code "Live Server" extension at http://127.0.0.1:5500

## Vulnerability / Technique
- **OWASP:** A03:2021 - Injection
- **MITRE ATT&CK:** T1059.007 - Command and Scripting Interpreter: JavaScript
- **Cross-Site Scripting (XSS) - DOM-based:** A vulnerability class where
  untrusted user input is inserted directly into the DOM without
  sanitization or encoding, causing the victim's own browser to execute
  attacker-controlled script or HTML as if it were legitimate page content.

## Steps to Reproduce

### Part 1 - Exploiting OWASP Juice Shop
1. Navigated to http://localhost:3000 and located the search bar.
2. Entered the payload into the search field:
```
   <iframe src="javascript:alert(`xss`)">
```
3. Pressed Enter. A JavaScript alert box fired immediately, displaying "xss".

   ![Payload entered into Juice Shop search bar]
   <img width="704" height="541" alt="image" src="https://github.com/user-attachments/assets/47add7a3-5f12-42b3-b143-34192865146d" />

   ![Alert popup confirming code execution]

   <img width="709" height="591" alt="image" src="https://github.com/user-attachments/assets/10ea4b2a-e799-437b-bd85-aa7079c0e113" />


5. Juice Shop confirmed the result directly: *"You successfully solved a
   challenge: DOM XSS."*
6. Inspected the page via DevTools (Elements tab) and located the injected
   element rendered live inside the DOM:
```html
   <span id="searchValue">
     <iframe src="javascript:alert(`xss`)">
   </span>
```
   This confirmed the application inserted raw, unsanitized user input
   directly into the page structure rather than encoding it as safe text.

   ![DevTools showing injected iframe inside the DOM](<img width="887" height="413" alt="image" src="https://github.com/user-attachments/assets/da2db83c-cdcf-4708-93be-5aff0a67b409" />
)

### Part 2 - Reproducing the vulnerability from scratch (custom PoC)
To confirm understanding of the underlying mechanism rather than just the
payload, three versions of a simple search feature were built and tested.

**Version 1 - Baseline (`search-demo.html`)**
Used `alert()` to display the search term:
```javascript
alert("You searched for: " + query);
```
Result: payload displayed as inert text inside the popup. Safe - but only
by accident, since `alert()` never parses input as HTML in the first place.

**Version 2 - Vulnerable (`Vulnerable-search-demo.html`)**
Used `innerHTML` to render the search term into the page:
```javascript
document.getElementById("results").innerHTML = "You searched for: " + query;
```
Result: submitting the same payload (`<iframe src="javascript:alert('xss')">`)
caused the browser to construct a real `<iframe>` element on the page,
which executed the injected `javascript:` URL - the alert fired exactly as
it did on Juice Shop, this time in a from-scratch reproduction.

![Vulnerable version executing the injected iframe]

<img width="781" height="388" alt="image" src="https://github.com/user-attachments/assets/24190854-af38-4fd9-a96b-62a0f3e8e1cd" />


**Version 3 - Secure fix (`Secure-search-demo.html`)**
Replaced `innerHTML` with `textContent`, and added an input sanitization
function stripping `<` and `>` characters as a second layer of defense:
```javascript
function sanitizeInput(str) {
  return str.replace(/[<>]/g, "");
}
const query = sanitizeInput(input.value.trim());
document.getElementById("results").textContent = "You searched for: " + query;
```
Result: the same payload rendered as plain, harmless text on the page:
```
You searched for: iframe src="javascript:alert('xss')"
```
No popup, no injected element, no code execution.

![Secure version neutralizing the payload as plain text](<img width="590" height="339" alt="image" src="https://github.com/user-attachments/assets/90e5d47f-6b20-4bdc-ade5-05543a415cf7" />
)

## Root Cause Analysis
Three different DOM methods were tested to understand exactly why this
vulnerability exists at the code level:

- `alert()` displays a string as an interactive popup in the browser UI.
  It never interprets its input as HTML or executable code - it only ever
  displays literal text, which is why the baseline version of the search
  function was safe by accident, not by design.
- `innerHTML` takes a string and tells the browser to parse it as live
  HTML/JavaScript, then rebuild the page's structure using it. Injecting
  an `<iframe>` or `<script>` tag through this method causes the browser
  to construct a real, functioning element on the page — disrupting the
  layout and, in this case, executing an attacker-controlled `javascript:`
  URL the moment the iframe attempted to load.
- `textContent`, used in the secure fix, forces the browser to treat all
  input as plain text only. HTML tags are displayed as harmless, literal
  characters on the page instead of being parsed or executed.

The root cause of the vulnerability is that the application used
`innerHTML` to render user-controlled input directly into the DOM without
sanitization, allowing attacker-supplied HTML/JavaScript to be interpreted
and executed as if it were legitimate page content. Unlike Day 1's SQL
injection, where the server-side database was tricked into running
malicious logic, XSS shifts the point of failure to the client side - the
victim's own browser becomes the execution engine, running
attacker-controlled code disguised as normal page content.

## Remediation
- **Use `textContent` instead of `innerHTML`** when inserting user-supplied
  data into the DOM, so input is always treated as plain text and never
  parsed as HTML.
- **Apply input sanitization** as defense-in-depth: use a regular
  expression or dedicated sanitization function to strip dangerous
  characters (e.g. `<`, `>`) before user input enters application logic,
  even when a safe rendering method is already in use.
- **Use a Content Security Policy (CSP)** header in production applications
  to restrict which scripts are allowed to execute on the page, as an
  additional layer of protection against injected code.
- **Avoid `javascript:` URLs entirely** in any user-facing input path, as
  they allow code execution simply by being loaded/rendered, without
  requiring a user click.
- **Encode output contextually** (HTML-encode when inserting into HTML,
  attribute-encode when inserting into an attribute, etc.) - most modern
  frameworks (React, Angular, Vue) do this by default, which is why raw
  DOM methods like `innerHTML` should be avoided unless absolutely
  necessary and explicitly reviewed.

## What I Learned
Today's biggest realization was that XSS and SQL injection share the same
root idea — untrusted input being treated as executable instructions
instead of plain data - but the two attacks target completely different
victims. SQL injection tricks the database on the server; XSS tricks the
browser on the victim's own machine, turning the user into an unwitting
participant in the attack.

Rebuilding the vulnerability from scratch, rather than only exploiting
someone else's app, made the difference between `alert()`, `innerHTML`,
and `textContent` concrete instead of abstract. Debugging my own broken
JavaScript along the way — a missing `id` attribute, a variable used
outside its scope - also reinforced that most real vulnerabilities aren't
exotic; they come from small, ordinary coding mistakes that nobody caught.
That's the mindset I want to carry into every piece of code I review going
forward: not "does this look dangerous," but "what exactly happens to user
input at every single step."

## Repository Structure
```
day-02-xss-juiceshop/
├── README.md
├── screenshots/
│   ├── step01-payload-entered.png
│   ├── step02-alert-popup.png
│   └── step03-devtools-dom-injection.png
└── custom-poc/
    ├── search-demo.html                  # baseline, safe by accident
    ├── Vulnerable-search-demo.html       # innerHTML, exploitable
    ├── Secure-search-demo.html           # textContent + sanitization, fixed
    └── screenshots/
        ├── vulnerable-version-alert-fires.png
        └── secure-version-payload-neutralized.png
```
