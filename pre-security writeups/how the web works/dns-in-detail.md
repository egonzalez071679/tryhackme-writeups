# DNS in Detail — TryHackMe

> **Path:** Pre-Security  
> **Difficulty:** Easy  
> **Category:** Networking  
> **Completed:** 2026  
> **Room Link:** https://tryhackme.com/room/dnsindetail

---

## Summary

Deep dive into the Domain Name System — how it translates domain names to IP addresses, the different record types, how the resolution process works step by step, and why DNS is one of the most abused protocols by attackers.

---

## Key Concepts Learned

- **DNS** — Translates human-readable domain names (e.g. `google.com`) into IP addresses machines can route to; like a phonebook for the internet
- **DNS record types** — A (IPv4), AAAA (IPv6), CNAME (alias/redirect), MX (mail server), TXT (verification, SPF, DKIM)
- **Resolution flow** — Device → Recursive resolver → Root servers → TLD servers → Authoritative nameserver
- **TTL (Time To Live)** — Controls how long a DNS answer is cached; low TTL = frequent lookups; high TTL = faster but slower to update
- **DNS as an attack vector** — DNS spoofing, DNS tunneling (data exfiltration), subdomain enumeration, C2 beaconing over DNS

---

## Notes & Walkthrough

### Task 1 — What is DNS?

DNS converts domain names to IP addresses. Without DNS, you'd need to remember the IP address of every site you visit.

### Task 2 — Domain Hierarchy
TLD (Top-Level Domain) There are two types of TLD, gTLD (Generic Top Level) and ccTLD (Country Code Top Level Domain). A ccTLD was used for geographical purposes.
Second-Level Domain, limited to 63 characters + can only use a-z 0-9 and hyphens.
Subdomain, length must be kept to 253 characters or less.

```
.                          ← Root
└── com.                   ← Top-Level Domain (TLD)
    └── tryhackme.com.     ← Second-Level Domain
        └── www.tryhackme.com.  ← Subdomain
```

### Task 3 — DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| `A` | Maps domain to IPv4 address | `tryhackme.com → 104.26.10.229` |
| `AAAA` | Maps domain to IPv6 address | `tryhackme.com → 2606:4700::...` |
| `CNAME` | Alias pointing to another domain | `www.tryhackme.com → tryhackme.com` |
| `MX` | Mail server for the domain | `tryhackme.com → mail.tryhackme.com` |
| `TXT` | Free-text; used for SPF, DKIM, site verification | `v=spf1 include:...` |

### Task 4 — Making a DNS Request

Step-by-step resolution flow:
1. Browser checks local cache
2. Asks recursive resolver (usually your ISP or `8.8.8.8`)
3. Resolver asks root nameserver (`.`)
4. Root refers to TLD nameserver (`.com`)
5. TLD refers to authoritative nameserver for `tryhackme.com`
6. Authoritative nameserver returns the IP
7. Resolver caches it for the TTL duration and returns it to your device

```bash
# Look up DNS records manually
nslookup tryhackme.com
dig tryhackme.com A
dig tryhackme.com MX
dig tryhackme.com TXT

# Check TTL on a record
dig tryhackme.com +ttlid
```

### Task 5 — Practical Exercise

Used the in-browser DNS tool to query different record types and trace the resolution path for a test domain.

---

## Tools Used

| Tool | Command | Purpose |
|------|---------|---------|
| `nslookup` | `nslookup <domain>` | Basic DNS record lookup |
| `dig` | `dig <domain> <type>` | Detailed DNS query with TTL and flags |

---

## Takeaways

- DNS is heavily abused: attackers use it for C2 beaconing (malware checks in via DNS queries), data exfiltration (DNS tunneling), and reconnaissance (subdomain enumeration)
- Abnormal DNS query patterns — high frequency, unusual record types, long subdomains — are key indicators of compromise in log analysis
- Understanding TTL helps interpret log timestamps: a cached record means the device may have resolved the domain much earlier than the log shows
- MX and TXT records are critical for email security analysis — SPF/DKIM failures in TXT records are common in phishing investigations

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/dnsindetail)
- [Cloudflare DNS Learning Center](https://www.cloudflare.com/learning/dns/what-is-dns/)
- [MITRE ATT&CK — DNS (T1071.004)](https://attack.mitre.org/techniques/T1071/004/)
