# TryHackMe — Pre-Security Writeups

> Documenting my hands-on learning through the TryHackMe Pre-Security path as part of my career transition from **Project Management** to **IR/DFIR Analyst**.

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red?style=flat-square)
![Path](https://img.shields.io/badge/Path-Pre--Security-blue?style=flat-square)
![Progress](https://img.shields.io/badge/Progress-5%2F25%20rooms-orange?style=flat-square)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=flat-square)

---

## What is the Pre-Security Path?

The TryHackMe Pre-Security path covers the foundational technical knowledge required before diving into offensive or defensive cybersecurity work. It runs entirely in the browser — no VPN or local setup needed.

It covers:

- **Networking** — how devices communicate, IP/MAC addressing, DNS, and protocols
- **How the Web Works** — HTTP, websites, DNS, and web requests
- **Linux Fundamentals** — navigating and using the Linux terminal
- **Windows Fundamentals** — the Windows OS, file system, and basic administration

This path is the prerequisite to the SOC Level 1 and Jr Penetration Tester paths, and feeds directly into the blue team skills needed for IR/DFIR work.

---

## My Background & Goal

| | |
|---|---|
| **Coming from** | Project Management |
| **Target role** | IR/DFIR Analyst |
| **Why Pre-Security** | Building the technical vocabulary and hands-on foundation that SOC and DFIR work requires |
| **Next step** | Cyber Security 101 → SOC Level 1 → Security+ → HTB CDSA → INE eCIR |

---

## Path Progress

```
Pre-Security:  ████████░░░░░░░░░░░░░░░░  5 / 31 rooms completed  ← currently active
```

### Currently Working On 🔥

**Pre-Security** — TryHackMe Learning Path
Covers the foundational technical knowledge required for all cybersecurity work — networking, how the web works, Linux, and Windows fundamentals.

`████████░░░░░░░░░░░░░░░░` 5 / 31 rooms completed

---

### Completed ✅

| # | Room | Category | Key Skills | Writeup |
|---|------|----------|------------|---------|
| 1 | [Defensive Security Intro](https://tryhackme.com/room/defensivesecurity) | 🛡 Blue Team | SOC, DFIR, Threat Intel, Malware Analysis | [Notes](rooms/defensive-security-intro.md) |
| 2 | [Careers in Cyber](https://tryhackme.com/room/careersincyber) | 💼 Career | Roles, Certs, IR/DFIR Path | [Notes](rooms/careers-in-cyber.md) |
| 3 | [What is Networking?](https://tryhackme.com/room/whatisnetworking) | 🌐 Networking | IP/MAC, Ping, ICMP, Data Flow | [Notes](rooms/what-is-networking.md) |
| 4 | [Offensive Security Intro](https://tryhackme.com/room/offensivesecurity) | ⚔️ Red Team | Ethical Hacking, gobuster, Recon | [Notes](rooms/offensive-security-intro.md) |
| 5 | [DNS in Detail](https://tryhackme.com/room/dnsindetail) | 🔍 Networking | DNS Records, Resolution, TTL, Spoofing | [Notes](rooms/dns-in-detail.md) |

### Up Next 🔜

#### How the Web Works
| Room | Category |
|------|----------|
| [HTTP in Detail](https://tryhackme.com/room/httpindetail) | 🌐 Web |
| [How Websites Work](https://tryhackme.com/room/howwebsiteswork) | 🌐 Web |
| [Putting It All Together](https://tryhackme.com/room/puttingitalltogether) | 🌐 Web |

#### Linux Fundamentals
| Room | Category |
|------|----------|
| [Linux Fundamentals Part 1](https://tryhackme.com/room/linuxfundamentalspart1) | 🐧 Linux |
| [Linux Fundamentals Part 2](https://tryhackme.com/room/linuxfundamentalspart2) | 🐧 Linux |
| [Linux Fundamentals Part 3](https://tryhackme.com/room/linuxfundamentalspart3) | 🐧 Linux |

#### Windows Fundamentals
| Room | Category |
|------|----------|
| [Windows Fundamentals Part 1](https://tryhackme.com/room/windowsfundamentals1xbx) | 🪟 Windows |
| [Windows Fundamentals Part 2](https://tryhackme.com/room/windowsfundamentals2x) | 🪟 Windows |
| [Windows Fundamentals Part 3](https://tryhackme.com/room/windowsfundamentals3x) | 🪟 Windows |

---

## Skills Gained So Far

### 🌐 Networking
- IP addressing — logical device identification used for routing across networks
- MAC addressing — hardware-level identification used within local networks
- DNS — record types (A, AAAA, CNAME, MX, TXT), full resolution flow, TTL and caching
- ICMP / ping — connectivity testing and what it looks like in logs
- How data flows between devices through routers and switches

### 🛡 Defensive Security
- SOC operations — 24/7 monitoring, alert triage, escalation workflows
- DFIR — incident investigation, evidence preservation, containment, and reporting
- Threat intelligence — understanding adversary tactics, tools, and motives
- Malware analysis — static and dynamic analysis to understand malicious behavior

### ⚔️ Offensive Security
- Ethical hacking mindset — attacking systems legally to expose weaknesses
- Web application reconnaissance with `gobuster`
- Why offensive knowledge makes you a better blue team defender

### 💼 Career & Context
- Clear understanding of the IR/DFIR Analyst role and its day-to-day responsibilities
- How Project Management skills transfer directly to incident response coordination
- Certification roadmap: SEC1 → SAL1 → Security+ → CDSA → eCIR

---

## Tools Introduced

| Tool | Command | Purpose |
|------|---------|---------|
| `ping` | `ping <target>` | Test device reachability via ICMP |
| `gobuster` | `gobuster dir -u <url> -w <wordlist>` | Brute-force hidden web directories |
| `nslookup` | `nslookup <domain>` | Query DNS records |
| `dig` | `dig <domain> <record-type>` | Detailed DNS queries with TTL info |

```

Each room writeup includes:
- Summary of what the room covers
- Key concepts learned with explanations
- Task-by-task notes and commands used
- Tools table with actual syntax
- Takeaways connecting the room to IR/DFIR work

---

## Connect

- 🔗 LinkedIn: [linkedin.com/in/esteban-gonzalez-3400ab233](https://www.linkedin.com/in/esteban-gonzalez-3400ab233)
- 💀 TryHackMe: [tryhackme.com/p/egonzalez.1679](https://tryhackme.com/p/egonzalez.1679)
