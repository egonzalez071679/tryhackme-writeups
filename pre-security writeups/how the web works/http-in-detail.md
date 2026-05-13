# HTTP in Detail — TryHackMe

> **Path:** Pre-Security (SEC0) — How the Web Works  
> **Difficulty:** Easy  
> **Category:** Web / Networking Fundamentals  
> **Completed:** 2026-05-04  
> **Room Link:** https://tryhackme.com/room/httpindetail

---

## Summary

Foundational room covering HTTP and HTTPS — the protocols that move nearly all web traffic. Walks through URL anatomy, request/response structure, HTTP methods, status codes, headers, and cookies. Builds directly on Client-Server Basics and is the prerequisite for nearly every web-security and SOC analyst skill that follows.

---

## Key Concepts Learned

- **HTTP (HyperText Transfer Protocol)** — the rules for client–server communication that power the web; developed by Tim Berners-Lee's team between 1989–1991
- **HTTPS** — HTTP encrypted with TLS; provides both confidentiality (eavesdroppers can't see content) and authenticity (you're talking to the real server, not an impersonator)
- **URL anatomy** — Scheme, User, Host, Port, Path, Query String, Fragment; each component has a specific role
- **Request structure** — method, path, protocol version, then headers, then a blank line, then optional body
- **Response structure** — status line, headers, blank line, then body
- **HTTP methods** — GET, POST, PUT, DELETE (the four most common); each carries different intent
- **Status codes by range** — 1xx informational, 2xx success, 3xx redirection, 4xx client error, 5xx server error
- **Headers** — extra metadata sent with requests (Host, User-Agent, Cookie, Content-Length, Accept-Encoding) and responses (Set-Cookie, Cache-Control, Content-Type, Content-Encoding)
- **Cookies as state on top of stateless HTTP** — `Set-Cookie` from server stores data in browser; the browser returns it on every subsequent request, letting the server "remember" the client

---

## Notes & Walkthrough

### URL anatomy

A URL is an instruction for accessing a resource. Each part of `http://user:password@tryhackme.com:80/view-room?id=1#task3` has a specific role:

| Component | Example value | Purpose |
|-----------|---------------|---------|
| **Scheme** | `http` / `https` / `ftp` | Which protocol to use |
| **User (optional)** | `user:password@` | Inline credentials for HTTP basic auth |
| **Host** | `tryhackme.com` | Domain name or IP of the server |
| **Port** | `:80` | Which port to connect to (80 = HTTP default, 443 = HTTPS default) |
| **Path** | `/view-room` | Resource location on the server |
| **Query string** | `?id=1` | Extra parameters sent to the path |
| **Fragment** | `#task3` | Anchor location within the page |

**SOC-relevant note:** inline credentials in URLs (`http://user:pass@host`) are deprecated and considered a phishing red flag — modern browsers warn or strip them, and seeing them in proxy logs is investigation-worthy.

### Making a request — minimum and richer forms

The smallest valid HTTP request is a single line:

```
GET / HTTP/1.1
```

This sends the GET method, requests the `/` path (homepage), and declares HTTP/1.1.

A more realistic request includes headers:

```
GET / HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0 Firefox/87.0
Referer: https://tryhackme.com/

```

Each line means something specific:

| Line | Meaning |
|------|---------|
| `GET / HTTP/1.1` | Method, path, protocol version |
| `Host: tryhackme.com` | Which website we want (servers can host many) |
| `User-Agent: ...` | Identifies the browser software/version |
| `Referer: ...` | The page that linked us here |
| *(blank line)* | Signals the end of the request |

### Response structure

A typical response from the server:

```
HTTP/1.1 200 OK
Server: nginx/1.15.8
Date: Fri, 09 Apr 2021 13:34:03 GMT
Content-Type: text/html
Content-Length: 98

<html>
<head>
 <title>TryHackMe</title>
</head>
<body>
 Welcome To TryHackMe.com
</body>
</html>
```

| Line / section | Meaning |
|----------------|---------|
| `HTTP/1.1 200 OK` | Protocol version + status code |
| `Server: nginx/1.15.8` | Server software and version |
| `Date: ...` | Server time and timezone |
| `Content-Type: text/html` | What kind of data is in the body |
| `Content-Length: 98` | Body size in bytes (so the client knows when it's complete) |
| *(blank line)* | Marks end of headers |
| HTML body | Actual content |

The room asked which response header tells the browser how much data to expect: **`Content-Length`**.

### HTTP methods

Four methods cover the vast majority of real traffic:

| Method | Purpose |
|--------|---------|
| **GET** | Retrieve information from the server |
| **POST** | Submit data to the server (often creating new records) |
| **PUT** | Submit data to update an existing resource |
| **DELETE** | Remove a resource |

Other methods exist (HEAD, OPTIONS, PATCH, CONNECT, TRACE) but rarely appear in everyday browsing.

### Status code ranges

| Range | Class | Meaning |
|-------|-------|---------|
| **100–199** | Information | Acknowledging the start of a request; rarely seen today |
| **200–299** | Success | Request worked |
| **300–399** | Redirection | Client should look elsewhere |
| **400–499** | Client error | Something wrong with the request itself |
| **500–599** | Server error | Server failure handling the request |

### Common status codes worth memorizing

| Code | Meaning |
|------|---------|
| **200 OK** | Successful request |
| **201 Created** | A new resource was created (POST often returns this) |
| **301 Moved Permanently** | Resource is now somewhere else; update bookmarks/search engines |
| **302 Found** | Temporary redirect |
| **400 Bad Request** | Malformed or missing data in the request |
| **401 Not Authorized** | You need to authenticate first |
| **403 Forbidden** | Authenticated, but not allowed |
| **404 Page Not Found** | Resource doesn't exist |
| **405 Method Not Allowed** | Wrong method for this resource (e.g., GET when POST was expected) |
| **500 Internal Server Error** | Generic server failure |
| **503 Service Unavailable** | Server is down or overloaded |

### Headers — what flows in each direction

Headers carry extra context. Some travel with the request, others with the response.

**Common request headers (browser → server):**

| Header | Purpose |
|--------|---------|
| `Host` | Tells the server which site you want (servers host many) |
| `User-Agent` | Browser software and version |
| `Content-Length` | How much data the request body contains |
| `Accept-Encoding` | What compression types the browser can handle (gzip, br, etc.) |
| `Cookie` | Previously-stored data the browser is sending back |

**Common response headers (server → browser):**

| Header | Purpose |
|--------|---------|
| `Set-Cookie` | Tells the browser to store this data for future requests |
| `Cache-Control` | How long to cache this response |
| `Content-Type` | What kind of data is in the body (HTML, JSON, image, video, etc.) |
| `Content-Encoding` | How the body has been compressed |

### Cookies and the stateless problem

HTTP itself is **stateless** — the server doesn't naturally remember anything between requests. Cookies are how applications layer state on top.

The flow:

1. Browser makes a request to `cookies.thm`
2. Server responds with content + `Set-Cookie: name=adam`
3. Browser stores `name=adam`
4. On every subsequent request, browser includes `Cookie: name=adam`
5. Server reads the cookie and "remembers" who the user is

This is how login sessions work. The cookie value is typically not the password itself — it's a **session token**, a long random string that the server can look up internally to identify the user.

**You can inspect cookies in browser DevTools (F12)** → Network tab → click any request → Cookies tab. Every cookie your browser is sending or receiving for that site is visible there.

---

## Tools Used

The room is conceptual. In real analyst work, the same concepts get exercised with:

| Tool | Purpose |
|------|---------|
| Browser DevTools (F12) | Inspect requests, responses, headers, cookies |
| `curl` | Send HTTP requests from the command line; full control over method, headers, body |
| Burp Suite / OWASP ZAP | Intercepting proxies for deep web traffic analysis |
| Wireshark | View HTTP at the packet level (only works for unencrypted HTTP, not HTTPS) |
| `nslookup` / `dig` | DNS resolution that has to happen before any HTTP request |

---

## Takeaways

- **HTTP status codes are foundational SOC vocabulary** — log analysis starts here. A spike of `404`s often signals enumeration; sudden `500`s can mean someone's exploiting a buggy endpoint; large numbers of `401`s could indicate brute force; `403`s after a successful login may mean privilege probing
- **The `User-Agent` header is one of the most-faked fields in attacker traffic** — malicious tools often present generic or mismatched User-Agents (curl/python-requests when they claim to be browsers, or outdated Firefox versions). User-Agent anomaly detection is a real SOC technique
- **`Host` header attacks are a real category** — Host header injection can be used to poison password reset flows, route requests to unintended backends, or pivot through proxies. Worth knowing the attack surface exists even at the HTTP fundamentals level
- **Cookies are the most-attacked HTTP element** — session hijacking, XSS, CSRF, session fixation, cookie tampering. All of these exploit the gap between *HTTP being stateless* and *applications needing state*. Understanding why cookies exist (the stateless problem) is the foundation for understanding why they're attacked
- **Unencrypted HTTP traffic is plaintext on the wire** — every header, every cookie, every body is visible to anyone in the path. This is why HTTPS exists and why "downgrading" to HTTP is a known attack pattern (SSL stripping). When investigating, plaintext credentials in HTTP logs are a finding
- **Content-Type and Content-Length matter for upload/download analysis** — file exfiltration over HTTP often shows as unusually large `Content-Length` on POST or unusual `Content-Type` (e.g., `application/octet-stream` to a non-file endpoint). DLP rules look at these fields
- **Status code 301/302 chains are an attacker technique** — phishing infrastructure often chains redirects to obscure final destinations and bypass URL filtering. Tracking the full redirect chain (not just the final URL) is part of phishing analysis
- **For Sec+ readiness:** request/response structure, status code ranges, common headers, and stateless-vs-stateful concepts are testable. Memorize 200/301/302/400/401/403/404/500/503 with their meanings

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/httpindetail)
- [RFC 9110 — HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [MDN Web Docs — HTTP Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [MDN — HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [MITRE ATT&CK — Web Protocols (T1071.001)](https://attack.mitre.org/techniques/T1071/001/)
- [MITRE ATT&CK — Steal Web Session Cookie (T1539)](https://attack.mitre.org/techniques/T1539/)
- Companion: [Client-Server Basics](../soc-level-1%20writeups/client-server-basics.md), [DNS in Detail](./dns-in-detail.md)
