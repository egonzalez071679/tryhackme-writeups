# TryHackMe — Pre-Security Writeups

> Documenting my hands-on learning through the TryHackMe Pre-Security path as part of my career transition from **Project Management** to **IR/DFIR Analyst**.

[![TryHackMe](https://tryhackme-badges.s3.amazonaws.com/egonzalez.1679.png)](https://tryhackme.com/p/egonzalez.1679)
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
Pre-Security:  ████████░░░░░░░░░░░░░░░░  5 / 25 rooms completed  ← currently active
```

### Currently Working On 🔥

**Pre-Security** — TryHackMe Learning Path
Covers the foundational technical knowledge required for all cybersecurity work — networking, how the web works, Linux, and Windows fundamentals.

`████████░░░░░░░░░░░░░░░░` 5 / 25 rooms completed

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

---

## How This Repo is Organized

```
tryhackme-pre-security/
│
├── README.md                        ← You are here
│
└── rooms/
    ├── TEMPLATE.md                  ← Blank template for new writeups
    ├── defensive-security-intro.md
    ├── careers-in-cyber.md
    ├── what-is-networking.md
    ├── offensive-security-intro.md
    └── dns-in-detail.md
```

Each room writeup includes:
- Summary of what the room covers
- Key concepts learned with explanations
- Task-by-task notes and commands used
- Tools table with actual syntax
- Takeaways connecting the room to IR/DFIR work

---

## How Pre-Security Fits My Roadmap

Pre-Security is the starting point — the foundation everything else builds on:

| Phase | When | How Pre-Security Is Used |
|-------|------|--------------------------|
| **Networking basics** | Now | IP, MAC, DNS, ICMP — the vocabulary every log analysis session requires |
| **Web fundamentals** | Now | HTTP, websites, requests — essential for understanding web-based attacks |
| **Linux & Windows** | Now | OS navigation and administration — core skills for hands-on DFIR investigations |

> Pre-Security feeds directly into Cyber Security 101 and SOC Level 1, where these fundamentals get applied to real defensive security scenarios.

---

## Broader Learning Roadmap

This Pre-Security path is Phase 1 of a structured 12–14 month plan:

| Phase | Platform | Focus | Target Certification |
|-------|----------|-------|----------------------|
| **1** — Now | TryHackMe + LabEx | Pre-Security + Linux practice | SEC1 |
| **2** — Month 4–6 | TryHackMe + LabEx + Self-study | SOC Level 1 + Security+ prep | SAL1 + Security+ |
| **3** — Month 6–10 | HTB Academy | SOC Analyst Path + Sherlocks | CDSA |
| **4** — Month 10–14 | INE | Incident Responder path | eCIR |

> Network+ content is studied as background material (Professor Messer — free) without sitting the exam, to build networking foundations for Security+.

---

## Connect

- 🔗 LinkedIn: [linkedin.com/in/esteban-gonzalez-3400ab233](https://www.linkedin.com/in/esteban-gonzalez-3400ab233)
- 💀 TryHackMe: [tryhackme.com/p/egonzalez.1679](https://tryhackme.com/p/egonzalez.1679)
