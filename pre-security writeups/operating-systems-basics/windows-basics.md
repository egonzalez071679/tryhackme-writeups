# Windows Basics — TryHackMe

> **Path:** Cyber Security 101 (SEC1) — Windows Fundamentals  
> **Difficulty:** Easy  
> **Category:** Operating Systems / Windows  
> **Completed:** 2026-05-12  
> **Room Link:** https://tryhackme.com/room/windowsfundamentals1xd

---

## Summary

First Windows-focused room in the SEC1 path. Covers the foundations every SOC and IR analyst needs for Windows endpoint investigation: the desktop and taskbar, account types and authentication, file system organization, application installation and updating, Windows Settings vs Control Panel, Task Manager, and native Windows security (Defender Antivirus + Defender Firewall). The hands-on lab uses Windows Server 2019.

---

## Key Concepts Learned

- **Windows account types** — Guest (minimal), Standard (everyday use), Administrator (full control)
- **Desktop components** — Taskbar, Start menu, Search, Task View, pinned apps, system tray, notifications
- **File Explorer and hierarchical folders** — Desktop, Documents, Downloads as primary directories
- **Application lifecycle** — installation (Microsoft Store, vendor installer .exe/.msi), updates (built-in vs third-party), uninstallation (Settings, Control Panel, vendor uninstaller)
- **Windows Update** — built-in OS and native-app update mechanism; security patches are the main reason it matters
- **Settings vs Control Panel** — modern Settings app (Windows 10+) vs legacy Control Panel (still required for some admin tasks)
- **Task Manager tabs** — Processes, Performance, Users, Details (with PIDs), Services
- **Windows Security dashboard** — Virus & threat protection, Firewall & network protection, App & browser control, Device security
- **Windows Defender Firewall profiles** — Domain (corporate), Private (trusted), Public (untrusted)
- **Inbound vs outbound firewall rules** with profile, status, and action attributes

---

## Notes & Walkthrough

### Account types and authentication

Windows authenticates users before granting desktop access. Each account has a permission level that determines what the user can do:

| Account type | What it can do | What it can't do |
|--------------|---------------|------------------|
| **Guest** | Minimal — temporary access only | Change system settings, install software, access other users' files |
| **Standard** | Run apps, change personal settings, browse | Install software system-wide, change system-wide settings |
| **Administrator** | Full system control — install software, change any setting, manage other users | (Nothing — admin is the privilege ceiling) |

UAC (User Account Control) prompts when an action requires elevation, even for admin accounts. This is intentional friction designed to catch silent privilege abuse.

### The Windows desktop

The room walks through the standard desktop layout — Desktop area, Taskbar at the bottom, Start menu, Search, Task View, system tray. None of it is exam-testable trivia on its own; it's the vocabulary needed to describe what an analyst sees during an investigation. When someone says "the user clicked the Start menu and ran cmd.exe" or "the suspicious shortcut was pinned to the taskbar," the audience needs to know what they mean.

### File Explorer and the file system

Windows organizes files hierarchically. Three primary user directories the room highlights:

- `C:\Users\<username>\Desktop`
- `C:\Users\<username>\Documents`
- `C:\Users\<username>\Downloads`

Within each, subfolders organize related content. The hierarchical structure scales — small environments and large ones use the same layout.

### Settings vs Control Panel

Two interfaces for system configuration, both still present in modern Windows:

| Interface | Style | Used for |
|-----------|-------|----------|
| **Windows Settings** | Modern (touch-friendly), centralized | Day-to-day configuration: Display, Network, Personalization, Update & Security, Apps |
| **Control Panel** | Legacy desktop UI | Older admin tools that haven't been migrated yet — some advanced firewall config, some device manager flows, certain administrative tools |

Microsoft has been migrating Control Panel functionality into Settings for years but the migration is incomplete. Both interfaces will continue to coexist for the foreseeable future.

### Installing, updating, and removing applications

**Installation paths:**
- **Microsoft Store** — curated, sandboxed, automatic updates. Safer source. Not available on Windows Server by default.
- **Direct vendor download** — `.exe` or `.msi` installer from the vendor's website. More flexibility, more responsibility for trust verification.

**Updates:**
- **Windows Update** handles the OS and native components
- **Built-in apps** often update silently in the background
- **Third-party apps** have their own update mechanisms — some prompt on launch, some require manual checks

**Uninstallation:**
- Microsoft Store for store-installed apps
- Settings → Apps → Installed apps (modern path)
- Control Panel → Programs → Uninstall a program (legacy path)
- Vendor's own uninstaller (sometimes the only correct way for complex software)

### Task Manager — the five tabs

Task Manager is the analyst's first stop when investigating what's running on a Windows host.

| Tab | What it shows |
|-----|---------------|
| **Processes** | Currently running apps and background processes, with CPU, memory, disk, and network per-process |
| **Performance** | Real-time graphs for CPU, memory, disk, GPU, network |
| **Users** | Currently logged-in users and their resource usage — useful on multi-user systems |
| **Details** | Lower-level view with PIDs (process IDs), user account context, command line if enabled |
| **Services** | Windows services and their running/stopped state |

Right-clicking a process gives options that matter for analyst work: **End task** (terminate), **Open file location** (find the binary on disk), **Properties** (see file details, signature, hash), and **Go to services** (jump to the service that spawned it, if applicable).

### Windows Security — the four pillars

The Windows Security dashboard consolidates the native security tools:

| Pillar | What it does |
|--------|--------------|
| **Virus & threat protection** | Windows Defender Antivirus — real-time scanning, scheduled scans, quarantine |
| **Firewall & network protection** | Windows Defender Firewall — inbound/outbound traffic rules per network profile |
| **App & browser control** | SmartScreen — reputation-based blocking of unsafe downloads and websites |
| **Device security** | TPM, Secure Boot status, kernel isolation, memory integrity |

### Windows Defender Firewall — the three profiles

The firewall applies a different rule set depending on the current network's profile:

| Profile | When used | Posture |
|---------|-----------|---------|
| **Domain** | System joined to a corporate Active Directory domain and connected to a recognized domain network | Typically more permissive — trusts internal services |
| **Private** | Trusted home or lab network | Permissive for local sharing, file/printer access |
| **Public** | Untrusted networks — coffee shop, airport WiFi | Strict — blocks most inbound by default |

**Advanced Settings** (accessible via the Control Panel firewall view) exposes the full rule editor:

- **Inbound rules** — what's allowed to reach this machine
- **Outbound rules** — what this machine is allowed to send out (often less restrictive by default)
- **Connection Security rules** — IPSec policies
- **Monitoring** — live rule and connection status

For each rule the columns expose: name, group, network profile (Domain/Private/Public/All), enabled status, action (Allow/Block), override (yes/no), and the program path it applies to.

### Hands-on — Windows Server 2019 lab

The lab attaches a Windows Server 2019 machine. The exercises walk through reading files in File Explorer, installing a basic application, and using Windows Security to run a scan. Nothing fancy — the point is to make the GUI vocabulary stick before SEC1 goes deeper into Windows internals (the Linux Fundamentals → Windows Fundamentals → Active Directory progression).

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Windows Settings (`ms-settings:`)** | Modern configuration interface; About PC, Update & Security, Apps |
| **Control Panel** | Legacy configuration interface; required for advanced firewall settings and some admin flows |
| **File Explorer** | Browse and manage the file system |
| **Task Manager** (Ctrl+Shift+Esc) | Real-time process, performance, and service monitoring |
| **Windows Security** | Dashboard for Defender Antivirus, Defender Firewall, SmartScreen, device security |
| **Windows Defender Firewall (Advanced Settings)** | Inbound/outbound rule editor (`wf.msc`) |
| **Windows Update** | OS and native-app patching |

---

## Takeaways

- **Account type is the first question in a Windows IR triage.** "Was the affected user a Standard or Administrator account?" determines what an attacker could actually do once they had control. A Standard-account compromise is bad; an Administrator-account compromise is worse and triggers a different response playbook. SOC analysts looking at Security log Event ID 4672 (special privileges assigned to new logon) check whether admin privileges were granted at logon — admin sessions deserve extra scrutiny.
- **The Microsoft Store vs direct-download distinction matters for malware investigations.** Most Windows malware enters via direct downloads (drive-by infections, cracked software, phishing attachments), not the Microsoft Store. When investigating a compromise, the install source of unexpected software is an important data point — store-installed software is rarely the root cause; vendor installers and one-off `.exe` downloads frequently are.
- **Windows Update gaps are an attacker's friend.** Unpatched systems are exploited at scale — EternalBlue (MS17-010) was patched in March 2017; WannaCry exploited unpatched systems in May 2017. SOC teams that monitor Windows Update compliance across the fleet catch lateral movement risk before it becomes incident response. CIS Benchmarks, NIST, and Sec+ Domain 4 all emphasize patch management as a top control.
- **Task Manager is the analyst's first window into a suspicious host — but it's not enough.** Task Manager shows what's running right now but not parent processes, command lines (without enabling), DLLs loaded, network connections per-process, or signed/unsigned status. For real investigation, analysts pivot to Sysinternals tools (Process Explorer, Process Monitor, Autoruns, TCPView) which expose what Task Manager hides. Recognizing Task Manager's limits is part of being effective on Windows.
- **The Details tab and PIDs matter for correlation.** When a Sysmon log says "Event ID 1, ProcessId 4892, CommandLine cmd.exe /c whoami", you need to be able to find PID 4892 in Task Manager's Details tab to confirm it's still running and tied to a specific user account. PID-based correlation is fundamental SOC work.
- **Windows Defender Firewall profiles are tested on Sec+ Domain 4.** Recognizing Domain vs Private vs Public profiles in scenarios is exam-testable. Real-world: misconfigured profiles are common — a laptop that's supposed to be on the Public profile but is set to Private is exposing services it shouldn't. SOC compliance scans flag profile misconfigurations as findings.
- **`wf.msc` (the Defender Firewall Advanced Settings UI) is one of the most-used Windows admin tools for SOC investigations.** When investigating outbound C2 traffic, the question "did the host firewall block this, or allow it?" lives here. Firewall rule modifications by an attacker (adding outbound allow rules for new ports) are a known persistence/preparation step — MITRE T1562.004 (Disable or Modify System Firewall).
- **Defender service tampering is a high-priority detection.** Event ID 5025 (Windows Firewall service stopped), Event ID 4950 (Firewall setting changed), and Microsoft Defender Antivirus tamper-protection alerts are common pre-ransomware indicators. Attackers disable defenses before deploying payloads. SOC teams monitor these events as critical-severity. MITRE T1562.001 (Disable or Modify Tools).
- **Windows Server 2019 is a representative analyst target.** Most enterprise Windows servers in production today are Server 2016, 2019, or 2022. The GUI navigation and tool locations transfer almost identically across these versions. The lab in this room uses 2019 — that vocabulary is directly applicable to real enterprise endpoint investigation.
- **GUI knowledge is necessary but not sufficient for Windows IR.** Real Windows incident response involves PowerShell (Get-Process, Get-Service, Get-WinEvent, Get-NetTCPConnection), the Sysinternals suite, and registry forensics via Registry Explorer. This room establishes the GUI foundation; SEC1's later Windows rooms build the deeper tooling. Recruiters for SOC L1 expect comfort with both the GUI and the equivalent CLI commands.
- **Sec+ Domain 4 (Security Operations) tests Windows administration scenarios.** Questions like "an analyst needs to inspect running processes on a Windows host" or "an administrator must restrict inbound traffic for a workstation" come from this content. Knowing where to click is exam-testable; knowing what each tool shows is the more durable skill.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/windowsfundamentals1xd)
- [MITRE ATT&CK — Impair Defenses: Disable or Modify Tools (T1562.001)](https://attack.mitre.org/techniques/T1562/001/)
- [MITRE ATT&CK — Impair Defenses: Disable or Modify System Firewall (T1562.004)](https://attack.mitre.org/techniques/T1562/004/)
- [MITRE ATT&CK — Abuse Elevation Control Mechanism: Bypass User Account Control (T1548.002)](https://attack.mitre.org/techniques/T1548/002/)
- [Microsoft — Windows Defender Firewall with Advanced Security](https://learn.microsoft.com/en-us/windows/security/operating-system-security/network-security/windows-firewall/)
- [Microsoft — Windows Security app overview](https://learn.microsoft.com/en-us/windows/security/operating-system-security/system-security/windows-defender-security-center/windows-defender-security-center)
- [Sysinternals Suite](https://learn.microsoft.com/en-us/sysinternals/) — the tooling SEC1 will move toward as Windows content deepens
- Companion writeup: Operating System Security (in this repo) — covers the conceptual CIA/authentication foundation that Windows authentication implements
