# How Websites Work — TryHackMe

> **Path:** Pre-Security (SEC0) — How the Web Works  
> **Difficulty:** Easy  
> **Category:** Web Fundamentals / Web Application Security  
> **Completed:** 2026-05-12  
> **Room Link:** https://tryhackme.com/room/howwebsiteswork

---

## Summary

Foundational room introducing how websites are built and where common web vulnerabilities live. Covers the front-end / back-end split, the three core web languages (HTML for structure, CSS for styling, JavaScript for behavior), and two introductory web vulnerabilities: **Sensitive Data Exposure** (information leaked in client-side source) and **HTML Injection** (unsanitized user input rendered as live markup). Hands-on tasks include viewing page source and triggering HTML injection in a demo form.

---

## Key Concepts Learned

- **Client-side vs server-side** — front-end is what the browser renders; back-end is the server processing requests
- **The three web languages** — HTML structures content, CSS styles it, JavaScript makes it interactive
- **HTML structure** — `<!DOCTYPE html>`, `<html>`, `<head>`, `<body>`, common tags (`<h1>`, `<p>`, `<button>`, `<img>`, `<form>`)
- **Attributes** — `class` (shared styling identifier), `id` (unique identifier), `src` (resource location)
- **Viewing page source** — right-click → View Page Source (Chrome) / Show Page Source (Safari) reveals all client-side code
- **JavaScript integration** — inline `<script>` tags or remote `<script src="...">`; event handlers like `onclick`, `onhover`
- **Sensitive Data Exposure** — sensitive information unintentionally left in client-delivered code (HTML comments, JavaScript files, hidden form fields)
- **HTML Injection** — vulnerability that occurs when user input is rendered as HTML without sanitization
- **Input sanitization** — the foundational defense: never trust user input; filter or escape before rendering or processing

---

## Notes & Walkthrough

### Front end vs back end

Every website has two halves:

- **Front end (client-side):** what your browser downloads and renders. HTML, CSS, JavaScript. The user can read all of it — "View Source" is built into every browser.
- **Back end (server-side):** what the server runs to handle requests. Databases, application logic, authentication, business rules. The user never sees this code directly.

The browser makes a **request** to the web server, the server returns a **response**, and the browser renders the result. Many things happen between those two events (DNS resolution, TCP handshake, TLS negotiation — all covered in earlier SEC0 rooms), but the request/response pattern is the heart of HTTP.

### HTML — the structure

HTML uses **elements** (also called tags) to define the structure of a page. A minimal HTML document:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>Heading</h1>
        <p>Paragraph text</p>
    </body>
</html>
```

What each part does:

| Tag | Purpose |
|-----|---------|
| `<!DOCTYPE html>` | Declares the document as HTML5 |
| `<html>` | Root element — everything else lives inside |
| `<head>` | Metadata about the page (title, links to CSS, etc.) — not rendered as content |
| `<body>` | What the user actually sees |
| `<h1>` to `<h6>` | Headings, largest to smallest |
| `<p>` | Paragraph |
| `<button>`, `<img>`, `<form>`, `<input>`, `<a>`, ... | Many more for buttons, images, forms, links, etc. |

#### Attributes

Tags carry **attributes** that modify their behavior or appearance:

```html
<p class="bold-text">Styled paragraph</p>
<img src="img/cat.jpg" alt="A cat">
<p id="user-greeting">Hello!</p>
```

Two important distinctions:

- **`class`** — can be reused on many elements (used for shared styling). Many `<p>` tags can have `class="bold-text"`.
- **`id`** — must be unique per page (used to identify a specific element, often by JavaScript). Only one element can have `id="user-greeting"`.

### JavaScript — the behavior

JavaScript adds interactivity. Without it, pages would be static documents. Two ways to include it:

```html
<!-- Inline -->
<script>
    document.getElementById("demo").innerHTML = "Hack the Planet";
</script>

<!-- External file -->
<script src="/location/of/javascript_file.js"></script>
```

A common JavaScript pattern: find an element by ID, change its content.

```javascript
document.getElementById("demo").innerHTML = "Hack the Planet";
```

This reads: "find the element whose ID is `demo`, and replace its inner HTML with the string 'Hack the Planet'."

#### Event handlers

JavaScript can react to user actions via event handlers:

```html
<button onclick='document.getElementById("demo").innerHTML = "Button Clicked";'>
    Click Me!
</button>
```

When the user clicks, the `onclick` handler runs, finds the element with `id="demo"`, and rewrites its content. Common event handlers: `onclick`, `onhover`, `onload`, `onsubmit`, `onchange`.

### Practical — viewing page source

The room walks through the **View Page Source** workflow on a deployed example. The browser delivers the entire client-side code (HTML, CSS, inline JavaScript) to the user — so anything in there is visible to anyone who visits the page.

Right-click → View Page Source (Chrome / Firefox) or Show Page Source (Safari) opens the raw HTML in a new tab. Inspecting it reveals every tag, attribute, comment, and inline script on the page.

### Sensitive Data Exposure

This is the first introductory web vulnerability in SEC0. The room provides a deliberately vulnerable form:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Fake Website</title>
    </head>
    <body>
       <form>
          <input type='text' name='username'>
          <input type='password' name='password'>
          <button>Login</button>
          <!-- TODO: remove test credentials admin:password123 -->
       </form>
    </body>
</html>
```

The vulnerability is the HTML comment:

```html
<!-- TODO: remove test credentials admin:password123 -->
```

A developer left themselves a reminder during testing and forgot to delete it before deploying. The credentials `admin:password123` are now delivered to every visitor's browser. Anyone who clicks "View Source" finds them.

**What gets exposed in real-world Sensitive Data Exposure cases:**

- Hardcoded credentials in HTML comments (the textbook example)
- API keys embedded in JavaScript files
- Hidden form fields containing internal IDs, debug flags, or pricing information
- Commented-out code blocks revealing alternative endpoints or removed features
- URLs to staging environments, internal admin panels, or backup hosts
- Software version strings useful for targeted exploit selection
- Error messages with stack traces, file paths, or database details

### HTML Injection

The room's second vulnerability — HTML Injection — happens when user-supplied input is rendered back to the page as live HTML instead of plain text.

The vulnerable pattern (from the room's "What's your name?" example):

```javascript
function sayHi() {
    const name = document.getElementById('name').value;
    document.getElementById("welcome-msg").innerHTML = "Welcome " + name;
}
```

The flow:

1. User types something into the `name` input field
2. The `sayHi` function reads the value
3. The function writes `"Welcome " + name` into the `welcome-msg` element using `.innerHTML`
4. **`.innerHTML` parses the assigned string as HTML, not as plain text**

If a user enters their actual name (`Fred Bloggs`), the page displays "Welcome Fred Bloggs" — as intended.

If a user enters HTML markup (`<h1>HACKED</h1>`), the browser renders it as a large heading on the page — not as the literal string `<h1>HACKED</h1>`. The injection succeeds because the application trusted the user's input and rendered it directly.

The room emphasizes: **never trust user input**. Anything the user controls — form fields, URL parameters, headers, cookies — can carry malicious content. Defenses include:

- **Output encoding / escaping:** convert `<`, `>`, `&`, `"`, `'` to their HTML-entity equivalents before rendering
- **Safe APIs:** use `.textContent` instead of `.innerHTML` when treating input as text
- **Allow-list filtering:** if HTML must be allowed, restrict to a known-safe set of tags and attributes
- **Content Security Policy (CSP):** browser-level header that restricts what scripts can execute even if injection succeeds

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Web browser (Chrome / Firefox)** | Render the page; View Source for client-side inspection |
| **View Page Source** (Ctrl+U / Cmd+Opt+U) | Inspect raw HTML, JavaScript, and inline CSS |
| **Browser DevTools** (F12) | Live element inspection, console for testing JavaScript, Network tab for request/response analysis |
| Conceptual reference: HTTP, DNS, OSI Model | From earlier SEC0 rooms |

---

## Takeaways

- **Anything in client-side code is public.** HTML, inline JavaScript, CSS, and even files referenced by `<script src="...">` are all delivered to every visitor. Treat client-side code as a public document — never include secrets there. This is foundational web security and Sec+ tests it under Domain 3.
- **HTML comments in production are a common finding in web app pentests.** Bug bounty reports regularly show credential leaks, API keys, and hidden endpoint references discovered through "View Source." SOC analysts investigating compromised web apps check page source for the same reasons.
- **Sensitive Data Exposure maps to OWASP A02:2021 — Cryptographic Failures** (previously "Sensitive Data Exposure" in the 2017 list). It's one of the top web application risks because the entry barrier is so low — you don't need any tooling beyond a browser to find it.
- **HTML Injection is the conceptual precursor to XSS.** Cross-Site Scripting (XSS) extends HTML injection by injecting executable JavaScript rather than just markup. The defense is the same in both cases: sanitize input, encode output, and use safe rendering APIs. MITRE maps XSS execution to T1059.007 — Command and Scripting Interpreter: JavaScript.
- **`.innerHTML` vs `.textContent` is a real defensive coding decision.** Using `.textContent` treats the assigned value as plain text, so `<h1>` is displayed literally instead of rendered. SOC analysts and code reviewers look for `.innerHTML` (and equivalents in other frameworks) as injection-risk hotspots during application security reviews.
- **Input sanitization is a Sec+ Domain 2 concept tested heavily.** The phrase "never trust user input" appears across multiple Sec+ scenarios — file uploads, URL parameters, form fields, API requests. Recognizing the pattern is more important than memorizing specific defenses.
- **Investigating a compromised web app starts with the same View Source instinct.** When triaging a defaced site, malware-distributing page, or suspected supply-chain JavaScript compromise, the analyst reads the rendered HTML and the loaded JavaScript first. Knowing what "normal" looks like for the application is half the battle.
- **Content Security Policy (CSP) is the structural defense against injection.** Even when HTML injection succeeds, a strict CSP header can prevent the injected content from executing scripts, loading external resources, or exfiltrating data. CSP appears in Sec+ Domain 3 (Architecture) and is a common interview question for web-app-adjacent roles.
- **HTML comments survive on attacker-controlled infrastructure too.** Threat hunters investigating phishing kits and credential harvesting pages often find developer comments inside the kit — sometimes including the attacker's notes, test credentials, or kit-source attribution. The same principle that exposes legitimate developers also exposes attackers.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/howwebsiteswork)
- [OWASP Top Ten — A02:2021 Cryptographic Failures](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)
- [OWASP Top Ten — A03:2021 Injection](https://owasp.org/Top10/A03_2021-Injection/)
- [MITRE ATT&CK — Command and Scripting Interpreter: JavaScript (T1059.007)](https://attack.mitre.org/techniques/T1059/007/)
- [MITRE ATT&CK — Drive-by Compromise (T1189)](https://attack.mitre.org/techniques/T1189/) — related: web pages weaponized through injection
- [MDN — `Element.innerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) — explicitly documents the security implications
- [MDN — Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- Companion writeups: HTTP in Detail, DNS in Detail (published in this repo)
