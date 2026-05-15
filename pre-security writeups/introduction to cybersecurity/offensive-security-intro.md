# Offensive Security Intro — TryHackMe

> **Path:** Pre-Security  
> **Difficulty:** Easy  
> **Category:** Red Team  
> **Completed:** 2026  
> **Room Link:** https://tryhackme.com/room/offensivesecurity

---

## Summary

First hands-on experience with the red team / attacker mindset. Covers what offensive security means, introduces ethical hacking, and walks through a practical web application hacking exercise using `dirb` to find hidden pages.

---

## Key Concepts Learned

- **Offensive security mindset** — Thinking like an attacker to find weaknesses before malicious actors do
- **Ethical hacking** — Authorized and legal hacking that operates within defined rules of engagement
- **Web application hacking** — Finding and exploiting vulnerabilities in websites — the most common attack surface in modern organizations
- **dirb** — a command-line web content scanner designed for security auditing and penetration testing. It is used to discover hidden files, directories, and pages on a web server by launching a dictionary-based, brute-force attack.
- **Reconnaissance** — The first phase of hacking; gathering information about a target before attempting exploitation

---

## Notes & Walkthrough

### Task 1 — What is Offensive Security?

Offensive security involves breaking into systems, exploiting bugs, and finding vulnerabilities — all with authorization. The goal is to understand attacker methods so defenders can protect against them.

### Task 2 — Hacking your First Machine

Practical exercise: used `dirb` to discover a hidden admin page on a fake banking website.

```bash
# dirb http://fakebank.thm

# What the flags mean:
# ---- Scanning URL: http://fakebank.thm/ ----
# (http://fakebank.thm/bank-transfer (CODE:200|SIZE:4663))
# (http://fakebank.thm/images (CODE:301|SIZE:179))
```

**Result:** Found `/bank-transfer` hidden admin page that was not linked anywhere on the site.

**Key insight:** Developers often leave admin pages, backup files, or config pages accessible but unlisted. Tools like dirb find them by trying common names from a wordlist.

### Task 3 — Careers in Offensive Security

Offensive roles include penetration tester, red teamer, and bug bounty hunter. Each requires authorization and operates within legal and ethical boundaries.

---

## Tools Used

| Tool | Command | Purpose |
|------|---------|---------|
| `dirb` | `dirb dirb http://fakebank.thm>` | Directory/file enumeration on web servers |

---

## Takeaways

- Understanding how attackers find hidden pages helps me write better detection rules as a defender
- `gobuster` scans generate distinctive HTTP traffic patterns — recognizing this in logs is a key SOC/DFIR skill
- The attacker methodology (recon → scan → exploit) maps to the Cyber Kill Chain I'll study in SOC Level 1
- Offensive knowledge makes you a sharper blue teamer — you know what to look for because you know how attacks work

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/offensivesecurity)
- [gobuster GitHub](https://github.com/OJ/gobuster)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
