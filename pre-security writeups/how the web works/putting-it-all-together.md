# Putting it all together — TryHackMe

> **Path:** Pre-Security (SEC0) — How the Web Works  
> **Difficulty:** Easy  
> **Category:** Web Fundamentals / Web Architecture  
> **Completed:** 2026-05-12  
> **Room Link:** https://tryhackme.com/room/puttingitalltogether

---

## Summary

Capstone room of the SEC0 "How the Web Works" module. Walks through every component that participates in a single web request — DNS resolution, load balancers, CDNs, WAFs, web servers, virtual hosts, databases, and back-end scripting — and assembles them into the complete 11-step flow that delivers a webpage to a browser. This is the room where DNS in Detail, HTTP in Detail, and How Websites Work get tied together into one coherent picture.

---

## Key Concepts Learned

- **The complete request flow** — from URL typed to rendered page, 11 distinct stages
- **Load balancers** — distribute traffic across multiple back-end servers; provide failover via health checks
- **Load balancing algorithms** — round-robin (each server in turn) and weighted (least-busy server)
- **CDN (Content Delivery Network)** — geographically distributed static-file hosting that reduces latency and origin load
- **Databases** — back-end storage; common examples MySQL, MSSQL, MongoDB, Postgres
- **WAF (Web Application Firewall)** — sits in front of web servers, filters malicious requests, performs rate limiting
- **Web server software** — Apache, Nginx, IIS, NodeJS; serves files from a root directory (`/var/www/html` on Linux, `C:\inetpub\wwwroot` on Windows IIS)
- **Virtual hosts** — multiple websites on one server, selected by matching the HTTP Host header
- **Static vs dynamic content** — static files served as-is; dynamic content generated per-request by back-end code
- **Back-end scripting languages** — PHP, Python, Ruby, NodeJS, Perl; the code that produces dynamic responses

---

## Notes & Walkthrough

### The big picture

Every web request involves more components than most users realize. The room frames the simple version first:

```
Browser request  →  DNS lookup  →  Web server  →  Page rendered
```

Then progressively layers in the real-world components: load balancers in front of multiple web servers, CDNs serving static assets, WAFs filtering malicious traffic, databases backing dynamic responses, and virtual hosts letting one server handle many domains.

### Load balancers

When a single web server can't handle the traffic — or when high availability matters — a **load balancer** sits in front of multiple back-end servers and distributes incoming requests across them.

Two main benefits:

- **Capacity** — spread load across many servers so no single one becomes the bottleneck
- **Failover** — if a server stops responding, the load balancer stops sending it traffic

**Distribution algorithms** the room covers:

| Algorithm | Behavior |
|-----------|----------|
| **Round-robin** | Each request goes to the next server in line; rotates through them in order |
| **Weighted (least-busy)** | Counts current connections per server; new request goes to the server with the fewest |

The load balancer continuously runs **health checks** — periodic probes that verify each server is responding correctly. A server that fails health checks gets removed from rotation until it recovers. This connects directly to the Networking Devices content from Lammle Ch.5 — load balancers operate at Layer 4 (TCP port-based distribution) or Layer 7 (HTTP-aware distribution that can route by URL path, cookies, etc.).

### CDN — Content Delivery Networks

A CDN hosts **static files** (JavaScript, CSS, images, videos) across thousands of servers globally. When a user requests one of those files, the CDN serves it from the geographically nearest edge server instead of the origin web server.

Two outcomes:

- **Lower latency for users** — bytes travel less distance
- **Lower load on the origin** — most static-file requests never reach it

Common CDN providers: Cloudflare, Akamai, AWS CloudFront, Fastly. In practice, a single website might serve HTML from its own origin web server while serving every image, stylesheet, and JavaScript bundle from a CDN edge.

### Databases

Web applications store data (user accounts, blog posts, shopping cart contents, etc.) in databases. The web server queries the database to retrieve or update information as part of generating dynamic responses.

Common database systems:

- **MySQL** — open-source relational; widely used in PHP/LAMP stacks
- **MSSQL (Microsoft SQL Server)** — Windows-oriented relational database
- **PostgreSQL** — open-source relational; strong feature set
- **MongoDB** — document-oriented NoSQL database; stores JSON-like documents

The choice of database affects what kinds of attacks it's vulnerable to (SQL injection targets relational databases; NoSQL injection targets document stores like MongoDB).

### WAF — Web Application Firewall

A WAF sits between incoming traffic and the web server, inspecting requests for attack signatures before they reach the application. It performs:

- **Attack pattern detection** — look for SQL injection payloads, XSS payloads, command injection attempts, known exploit signatures
- **Bot detection** — distinguish real browsers from automated scrapers and attack tools
- **Rate limiting** — cap the number of requests per IP per second to mitigate brute force and DoS attempts
- **Blocking** — drop requests deemed malicious; the back-end server never sees them

WAFs operate at Layer 7 (HTTP application layer). Common products: Cloudflare WAF, AWS WAF, F5 BIG-IP ASM, ModSecurity (open-source).

### Web servers — Apache, Nginx, IIS, NodeJS

A web server is software that listens for incoming HTTP(S) connections and returns content. The four most common:

| Server | Platform | Default Web Root |
|--------|----------|------------------|
| **Apache** | Cross-platform, often Linux | `/var/www/html` |
| **Nginx** | Cross-platform, often Linux | `/var/www/html` |
| **IIS** | Windows | `C:\inetpub\wwwroot` |
| **NodeJS** | Cross-platform, JavaScript runtime | Configured per app |

A request for `http://www.example.com/picture.jpg` triggers the server to serve `<web_root>/picture.jpg` — `/var/www/html/picture.jpg` on a default Apache/Nginx install.

### Virtual hosts

A single physical web server can host many websites with different domain names. **Virtual hosts** make this work:

1. Each website is configured as a virtual host (a text-based config file in the server software)
2. Each virtual host maps a hostname to a root directory
3. When a request arrives, the server reads the HTTP `Host:` header
4. The server matches that hostname against its virtual host config and serves from the matching root

Example:

```
one.com → /var/www/website_one
two.com → /var/www/website_two
```

If no virtual host matches the incoming Host header, the server serves a configured default site. **No limit** on how many virtual hosts a single server can have.

### Static vs dynamic content

**Static content:** files served directly from disk without modification. Pictures, JavaScript files, CSS files, plain HTML that never changes. Same bytes go to every visitor.

**Dynamic content:** generated per-request by code on the back end. Different visitors get different output based on context — logged-in user, search query, browser language, etc.

The room's example: a blog homepage shows the latest entries. When a new post is published, the homepage updates automatically because it's generated dynamically each time someone visits — pulling current entries from a database.

The user's browser **only sees the rendered HTML output**, not the back-end code that produced it. The server-side code (PHP, Python, etc.) runs on the server and the result is delivered to the browser.

### Back-end scripting languages

Common server-side languages:

- **PHP** — historically dominant in web apps; powers WordPress and much of the web
- **Python** — Django and Flask frameworks; common in modern web apps and APIs
- **Ruby** — Rails framework; popular with startups
- **NodeJS** — JavaScript on the server; popular for real-time and API-heavy apps
- **Perl** — legacy but still present in some environments

The room shows a minimal PHP example:

```php
<html><body>Hello <?php echo $_GET["name"]; ?></body></html>
```

When a user visits `http://example.com/index.php?name=adam`, the server runs the PHP, substitutes `adam` into the output, and sends:

```html
<html><body>Hello adam</body></html>
```

The browser never sees the `<?php ... ?>` tags — only the final HTML.

**Critical insight from the room:** this pattern is the foundation of countless web vulnerabilities. If a developer takes user input (the `name` parameter) and includes it in output (or worse, in a database query) without sanitization, attackers can inject HTML, JavaScript, or SQL into the response or the back-end query. This is the conceptual bridge to HTML Injection (covered in How Websites Work) and SQL Injection (covered in later rooms).

### The complete request flow

Putting all the components together, here's what actually happens when a user types a URL and hits enter:

1. **Request a website in the browser** — user types the URL or clicks a link; the browser begins resolving the resource
2. **Check local cache for the IP address** — browser cache and OS resolver cache checked first
3. **Check the recursive DNS server** — if cache misses, query the configured resolver (ISP's resolver or a public one like 8.8.8.8 / 1.1.1.1)
4. **Query a root server to find the authoritative DNS server** — recursive resolver walks the hierarchy (root → TLD → authoritative) if it doesn't have the record cached
5. **The authoritative DNS server provides the IP address** — final answer returned, including any CNAME chains resolved along the way
6. **The request passes through a Web Application Firewall (WAF)** — incoming HTTP(S) traffic filtered for attack signatures before reaching the application
7. **The request passes through a Load Balancer** — distributes the request to one of the healthy back-end servers
8. **The browser connects to the web server on port 80 or 443** — TCP three-way handshake, then TLS handshake if HTTPS
9. **The web server receives the GET request** — accepts the request, matches the Host header to a virtual host, routes to the appropriate handler
10. **The web application communicates with the database** — if the response requires dynamic data, the back-end queries one or more databases
11. **The browser renders the HTML into a viewable website** — response delivered, browser parses HTML/CSS/JavaScript, page rendered for the user

Each layer can be missing in a simpler architecture (no CDN, no WAF, single server with no load balancer), but enterprise deployments typically include all of them.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Web browser (Chrome / Firefox)** | Render the page; DevTools Network tab shows each request/response in the flow |
| **Browser DevTools — Network tab** | Inspect the full request lifecycle, including DNS timing, TLS handshake, and response headers |
| **`dig` / `nslookup`** | Manually query DNS to see what the recursive resolver returns |
| **`curl -v`** | Issue raw HTTP requests, including custom Host headers to test virtual hosts |
| Conceptual reference: DNS in Detail, HTTP in Detail, How Websites Work | All from earlier SEC0 rooms |

---

## Takeaways

- **The 11-step flow is the entire web stack in miniature.** Every layer exposes a different surface that SOC analysts and IR analysts investigate — DNS logs for resolution patterns, WAF logs for blocked attack signatures, load balancer logs for cross-server attack reconstruction, web server logs for individual request analysis, database query logs (when enabled) for injection investigations. Knowing which layer holds which evidence is foundational for IR work.
- **Load balancer logs are the only complete view of attacks against clustered services.** When a web application is fronted by a load balancer distributing requests across many back-end servers, no single back-end server has the full attack timeline — the load balancer does. Pulling load balancer logs first, then correlating to specific back-end servers, gives the complete picture of an attack against a clustered application.
- **CDNs change the threat model significantly.** A CDN sits between the user and the origin, so the origin server may never see the attacker's real IP — it sees the CDN's edge IP. SOC analysts investigating attacks against CDN-fronted sites must request logs from the CDN provider (Cloudflare, Akamai, etc.) to recover original source IPs. Without CDN cooperation, attribution is much harder.
- **WAFs are central to web application defense — and a known attacker target.** Attackers actively probe WAFs to identify which rules they enforce, then craft payloads to bypass them. WAF bypass techniques (case variation, unicode encoding, HTTP parameter pollution, malformed requests) are documented in OWASP and standard pentest methodology. SOC teams monitor WAF logs not just for blocks but for **patterns of bypass attempts** — repeated failed encoding tricks against the same endpoint indicates an attacker is actively probing.
- **Virtual host configuration is a known misconfiguration target.** Default virtual host fallback — what happens when no virtual host matches the Host header — is a common source of information disclosure. A misconfigured server may serve internal admin pages, debug endpoints, or staging content to anyone who sends an arbitrary Host header. Tools like Nikto and dirb test for this systematically.
- **Static vs dynamic content matters for IOC tracking.** Compromised websites often have malicious JavaScript injected into static files (skimmers like Magecart) or back-end code modified to serve malicious dynamic content. Distinguishing static vs dynamic helps SOC analysts know where to look — file integrity monitoring on static assets, code-level review on dynamic code paths.
- **PHP `$_GET` and equivalent user-input patterns are the source of most web vulnerabilities.** Cross-Site Scripting (T1059.007), SQL Injection (CWE-89), Command Injection (T1059), Server-Side Request Forgery, Server-Side Template Injection — all of them ultimately involve user-supplied input flowing into a sensitive operation without proper sanitization. Sec+ tests this concept across multiple domains; CDSA and real SOC work test recognition of these patterns in actual logs.
- **Database choice influences attack surface.** SQL injection (CWE-89) targets relational databases (MySQL, MSSQL, PostgreSQL). NoSQL injection targets document stores like MongoDB with different but conceptually similar payload patterns. ORM frameworks (Django ORM, Active Record, Sequelize) reduce but don't eliminate these risks. Knowing the database technology in use during an IR engagement narrows the relevant attack patterns to consider.
- **Rate limiting at the WAF layer is the first defense against credential stuffing and brute force.** When investigating account compromise, the question "did the WAF rate-limit this IP?" is often the difference between "the attacker tried 10,000 passwords" and "the attacker tried 3 passwords because they got blocked." Both outcomes are findings; both require different response actions.
- **The complete flow is a classic interview question.** "Walk me through what happens when a user types google.com and hits enter" is asked in nearly every web-adjacent SOC and security engineering interview. Being able to narrate the 11 steps with each layer named correctly — and what could go wrong at each — is a quick credibility test in technical interviews.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/puttingitalltogether)
- [OWASP Top Ten — A03:2021 Injection](https://owasp.org/Top10/A03_2021-Injection/)
- [OWASP Top Ten — A05:2021 Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)
- [OWASP — Web Application Firewall](https://owasp.org/www-community/Web_Application_Firewall)
- [MITRE ATT&CK — Command and Scripting Interpreter: JavaScript (T1059.007)](https://attack.mitre.org/techniques/T1059/007/)
- [MITRE ATT&CK — Brute Force: Password Spraying (T1110.003)](https://attack.mitre.org/techniques/T1110/003/)
- [MITRE ATT&CK — Exploit Public-Facing Application (T1190)](https://attack.mitre.org/techniques/T1190/)
- [CWE-89 — SQL Injection](https://cwe.mitre.org/data/definitions/89.html)
- Companion writeups in this repo: DNS in Detail, HTTP in Detail, How Websites Work — together with this room, they form the complete SEC0 "How the Web Works" module
