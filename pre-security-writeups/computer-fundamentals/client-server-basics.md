# Client-Server Basics — TryHackMe

> **Path:** SOC Level 1 (SEC1) — Cyber Security 101 / Web Hacking Basics  
> **Difficulty:** Easy  
> **Category:** Web / Networking Fundamentals  
> **Completed:** 2026-05-04  
> **Room Link:** https://tryhackme.com/room/clientserverbasics

---

## Summary

Foundational room covering the client-server model that underpins nearly every web interaction. Walks through what a client and server are, how they communicate via protocols and ports, how DNS resolves names to IP addresses, and how HTTP/HTTPS structures requests and responses. Sets the conceptual baseline for every later web-security room in the SEC1 path.

---

## Key Concepts Learned

- **Client-server model** — the asymmetric architecture where one party (client) requests resources and another (server) provides them; underpins HTTP, email, DNS, file shares, and most network services
- **Request and response** — every client-server interaction is a structured exchange; the server's response can carry success data or an error if the request was malformed or the resource unavailable
- **Protocol** — the set of rules defining how a client and server understand each other (commands, request structure, syntax, response formats, error handling)
- **Port** — a numerical endpoint identifying which service on a server a client wants to talk to; a single server runs many services on different ports
- **DNS (Domain Name System)** — resolves human-readable names into IP addresses so clients know where to send requests
- **HTTP / HTTPS** — the stateless client-server protocol that powers the World Wide Web; "stateless" means each request is processed independently without server memory of prior requests
- **Statefulness via cookies/sessions** — applications layer state on top of stateless HTTP using session identifiers (cookies, tokens) so users don't have to re-authenticate every request
- **HTTP methods** — GET, POST, PUT, DELETE, PATCH, HEAD, OPTIONS, CONNECT, TRACE; nine core commands that define the action a client wants the server to perform

---

## Notes & Walkthrough

### Task 1 — The client-server model

In the simplest terms: **the client requests, the server provides.**

Most familiar example: opening a web browser to visit a site.
- **Client:** the browser (Chrome, Firefox, etc.)
- **Server:** the system hosting the website
- **Request:** "give me the contents of /index.html"
- **Response:** the HTML, CSS, and assets that make up the page

The model is asymmetric — the server has resources to share; the client wants them. The same pattern applies far beyond browsers: email clients to mail servers, file managers to file shares, DNS resolvers to DNS servers, etc.

### Task 2 — Request and response

Every client-server interaction is a **structured exchange**. The client sends a request following the protocol's rules; the server replies with a response.

A response can be:
- **Successful** — carries the requested resource and metadata
- **Erroneous** — carries an error code if the request was malformed or the resource unavailable

The error possibility is critical for SOC analysts: when investigating intrusions, looking at *response codes* (200, 401, 403, 404, 500, etc.) often tells you what an attacker tried and whether they succeeded.

### Task 3 — Protocol

A **protocol** is the agreement between client and server about how to communicate. A protocol defines:

| Aspect | Example |
|--------|---------|
| Which commands both sides understand | HTTP's `GET`, `POST`, `DELETE` |
| How a request is structured | Command, then path, then headers, then body |
| What syntax is used | English-like command names, specific delimiters |
| What response is given to which request | `200 OK` for success, `404 Not Found` for missing resource |
| What response is given to faulty requests | `400 Bad Request` for malformed input |

Without a shared protocol, the client and server are speaking different languages. Real-world protocols (HTTP, SMTP, FTP, SSH) are formalized in **RFC documents** that both sides implement against.

### Task 4 — Port

A **port** is a numerical identifier (0-65535) that specifies which service on a server a client wants to reach. A single physical server can run many services simultaneously, each on a different port:

- Port 80 → HTTP web server
- Port 443 → HTTPS (encrypted) web server
- Port 22 → SSH remote shell
- Port 25 → SMTP email
- Port 53 → DNS

When a client connects, it must specify (or default to) the right port. Wrong port = wrong service = connection refused or wrong response.

### Task 5 — DNS (Domain Name System)

**DNS resolves human-readable names to IP addresses.**

Computers route traffic by IP address, not by name. When you type `tryhackme.com`, the browser doesn't know where to send the request until DNS converts the name to an IP like `104.26.10.229`.

The IP address is the actual "address of a server" in computer terms — DNS is the translator that lets humans use friendly names instead of memorizing numbers.

### Task 6 — HTTP / HTTPS

**HTTP (Hypertext Transfer Protocol)** is the stateless client-server protocol that powers the World Wide Web. **HTTPS** is HTTP with TLS encryption.

**Stateless** means: each request is processed independently. The server has no memory of previous requests from the same client. Every interaction starts from a clean slate.

This raises a question: **how do websites remember you're logged in?**

Answer: through **session mechanisms layered on top of HTTP**. When you log in:
1. Server generates a unique session identifier (e.g., a long random string)
2. Server sends it back to your browser as a cookie or token
3. Your browser includes that identifier in every subsequent request
4. Server uses the identifier to look up "this is Esteban, logged in 5 minutes ago"

Without this, you'd need to re-authenticate on every page load — because HTTP itself remembers nothing.

### Task 7 — HTTP methods

In HTTP terminology, **commands are called methods**. There are 9 core methods defined by the RFCs:

| Method | Purpose |
|--------|---------|
| **GET** | Retrieve a resource |
| **POST** | Submit data to create or process |
| **PUT** | Replace a resource entirely |
| **DELETE** | Remove a resource |
| **PATCH** | Modify part of a resource |
| **HEAD** | Like GET but returns only headers (no body) |
| **OPTIONS** | Ask what methods are allowed for a resource |
| **CONNECT** | Set up a tunnel (often for HTTPS through a proxy) |
| **TRACE** | Echo the request back (used for diagnostics) |

### Task 8 — The GET method in detail

**GET** retrieves a resource from a web server. Example:

```
GET https://tryhackme.com/index.php
```

This request can be broken down into recognizable fields:

| Field | Meaning |
|-------|---------|
| **Scheme** | Which protocol was used: `http` or `https` |
| **Host** | The name of the host we're requesting from (`tryhackme.com`) |
| **Filename** | Which file we requested (`/index.php`); a bare `/` translates to the default, usually `/index.html` |
| **Address** | The IP address where the resource is hosted (e.g., `127.0.0.1` for localhost) |
| **Status** | Server's response code — `200 OK` means success |

When the response comes back, it's split into two parts:
- **Response header** — metadata (status code, content type, server info, cookies, caching directives, etc.)
- **Response body** — the actual content (HTML, JSON, image bytes, etc.)

---

## Tools Used

The room is conceptual — no command-line tools required. But analysts working with these concepts in real environments use:

| Tool | Purpose |
|------|---------|
| Browser DevTools (F12) | Inspect HTTP requests, responses, headers, cookies |
| `curl` | Send HTTP requests from the command line; control method, headers, body |
| `wget` | Download files via HTTP/HTTPS |
| Burp Suite / OWASP ZAP | Intercepting proxies for deep request/response analysis |
| `nslookup` / `dig` | DNS resolution from CLI |

---

## Takeaways

- **HTTP response codes are foundational SOC vocabulary** — `401` (unauthorized), `403` (forbidden), `404` (not found), `500` (server error), `503` (service unavailable). Web log analysis starts with reading these codes and correlating patterns. A flood of `404`s often signals enumeration; a sudden spike in `500`s can indicate an attempted exploit
- **Port-to-service mapping is what separates network noise from signal** — when reading firewall logs, knowing port 22 = SSH, 3389 = RDP, 445 = SMB, 53 = DNS lets you reason about traffic without lookup. Unusual port use (HTTP on 8080, RDP on 13389) is a hunting opportunity
- **DNS is one of the most-abused protocols by attackers** — DNS tunneling exfiltrates data over port 53, malware uses DNS for C2 beaconing (T1071.004 — DNS), and DGA (domain generation algorithms) create rotating attacker domains. The "boring" protocol that resolves names is also the one that hides the most traffic
- **Stateless HTTP and session mechanisms create attack surface** — session tokens are what attackers steal in session hijacking, XSS, and CSRF attacks. Understanding *why* sessions exist (because HTTP is stateless) is the foundation for understanding *how* they're attacked
- **Method-based detection is a real signal** — most legitimate web traffic is GET and POST. Seeing PUT, DELETE, or PATCH against unexpected endpoints can indicate API abuse or attempts to manipulate server-side resources. WAFs alert on this
- **For DFIR work**: web server logs (Apache, Nginx, IIS) are ranked by triage urgency by analysts who can read HTTP traffic in their head. This room is the entry point to that fluency
- **The TRACE method has a specific security relevance** — it can be exploited via Cross-Site Tracing (XST) to leak HTTP headers including cookies. Many web servers disable TRACE by default; spotting it enabled in a configuration review is a finding

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/clientserverbasics)
- [RFC 9110 — HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [MDN Web Docs — HTTP Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [MITRE ATT&CK — Application Layer Protocol: DNS (T1071.004)](https://attack.mitre.org/techniques/T1071/004/)
- [MITRE ATT&CK — Web Protocols (T1071.001)](https://attack.mitre.org/techniques/T1071/001/)
- Companion: [DNS in Detail](../pre-security%20writeups/dns-in-detail.md), [Packets & Frames](../pre-security%20writeups/packets-and-frames.md)
