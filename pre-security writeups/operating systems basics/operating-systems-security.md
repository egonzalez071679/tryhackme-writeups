# Operating Systems Security — TryHackMe

> **Path:** Pre-Security (SEC0) — How Computers Work / Operating Systems  
> **Difficulty:** Easy  
> **Category:** Computing Fundamentals / Security Foundations  
> **Completed:** 2026-05-05  
> **Room Link:** https://tryhackme.com/room/operatingsystemsecurity

---

## Summary

Foundational room introducing operating system security concepts (CIA triad, authentication factors, weak passwords, file permissions, malicious programs) and pairing the theory with a practical SSH lab on a Linux target. The lab demonstrates remote authentication, basic command-line navigation, and one of the most important DFIR techniques on a Linux host — reading bash history.

---

## Key Concepts Learned

- **The CIA triad** — Confidentiality, Integrity, Availability — the three properties every security control protects (or that every attack tries to break)
- **Three authentication factors** — something you know (password/PIN), something you are (biometrics), something you have (token, phone)
- **Common-password risk** — weak passwords like `123456`, `qwerty`, `password` dominate breach data; password reuse compounds the problem across services
- **Principle of least privilege** — users and processes get only the access they actually need; weak file permissions break confidentiality and integrity simultaneously
- **Malicious program categories** — Trojans (attack confidentiality + integrity by giving attackers access), ransomware (attacks availability through encryption), each mapping to specific CIA properties
- **SSH (Secure Shell)** — encrypted remote access protocol over TCP/22; the standard way to access remote Linux systems
- **bash_history as evidence** — every command run in an interactive bash shell is stored in `~/.bash_history`, making it one of the highest-value DFIR artifacts on a compromised Linux host

---

## Notes & Walkthrough

### The CIA triad

Every security concern reduces to one (or more) of three goals:

| Goal | What it protects | What an attack on it looks like |
|------|-----------------|---------------------------------|
| **Confidentiality** | Sensitive information stays only with intended recipients | Data theft, eavesdropping, unauthorized read access |
| **Integrity** | Data and system behavior aren't altered without authorization | Tampering, defacement, log manipulation, backdoor installation |
| **Availability** | Systems and data are reachable when needed | Ransomware, DDoS, system destruction, account lockout |

The triad is the lens analysts use to categorize attacks during reporting — "this incident violated confidentiality and integrity" is more precise than "this was bad."

### Authentication factors

Three categories, each with different strengths:

- **Something you know** (passwords, PINs, security questions) — easy to deploy, but weakest because it can be guessed, phished, or leaked
- **Something you are** (fingerprints, face recognition, voice) — hard to fake, but irrevocable if compromised
- **Something you have** (hardware tokens, phone for SMS, authenticator app) — adds physical possession requirement

**Multi-factor authentication (MFA)** combines two or more of these categories. Combining two from the *same* category (like password + security question) is not true MFA — it's two-step verification within "something you know."

### Why weak passwords matter

The room shows a snippet of NCSC's top common passwords list:

```
1.  123456
2.  123456789
3.  qwerty
4.  password
5.  111111
...
```

These appear so often that automated attacks try them first. Combined with **password reuse across services**, a single breach can cascade: once an attacker has your `password1` from a data breach, they try it everywhere else.

### Weak file permissions

The Linux model assigns read/write/execute permissions to owner / group / others on every file. When permissions are set too loosely:

- **Confidentiality breaks:** users who shouldn't see a file can read it
- **Integrity breaks:** users who shouldn't edit a file can modify it

The principle of least privilege is the mitigation: each file should be readable/writable only by the principals who genuinely need that access.

### Malicious programs and the CIA mapping

Different malware classes attack different CIA properties:

| Malware type | Primary CIA target | Example impact |
|--------------|-------------------|----------------|
| **Trojan** | Confidentiality + Integrity | Attacker reads files, modifies records |
| **Ransomware** | Availability (then optionally Confidentiality if exfiltration is involved) | Files encrypted, user locked out until ransom paid |
| **Rootkit** | Integrity (system behavior trust) | Process listings, logs, kernel structures all altered to hide attacker activity |
| **Spyware** | Confidentiality | Keystrokes, screenshots, browsing history exfiltrated |

### Practical lab — SSH onto the target and find the flag

The lab provides a target machine and credentials. The objective: SSH into the target, find a flag file in the root user's home directory.

#### Step 1 — Confirm local context

```bash
whoami
```

Confirms which user the AttackBox is running as before connecting outward.

#### Step 2 — Connect via SSH

```bash
ssh USERNAME@MACHINE_IP
```

SSH negotiated the connection over TCP/22, authenticated with the provided password, and dropped into a remote shell on the target. From this point forward, every command runs on the remote machine, not the AttackBox.

#### Step 3 — Orientation on the remote system

```bash
ls
```

Listed the current directory's contents to understand where the session landed.

#### Step 4 — The path-typo recovery

This is where the lab got messy in a useful way. Several attempts to read the flag failed:

| Command tried | What went wrong |
|---------------|-----------------|
| `cat root flag.txt` | Treated `root` and `flag.txt` as two separate files; `root` doesn't exist as a file in the current directory |
| `flag.txt` | Tried to execute `flag.txt` as a command — bash returned "command not found" |
| `cd /root/root` | No such directory |
| `cd root` | No such directory at the current location |

The recovery: run `ls` again to confirm what's actually present, then read the file with the correct name in the correct directory:

```bash
cat flag.txt
```

The flag was retrieved.

**Why this matters:** the wrong commands aren't a failure — they're a normal part of working at the command line. The skill being practiced isn't "type the right command on the first try"; it's "recognize when something didn't work and pivot to a different approach."

#### Step 5 — Reading bash history

```bash
history
```

The `history` command displays the commands previously run in the current shell session, numbered in order. The same data is persisted in `~/.bash_history`, which survives across sessions:

```bash
cat ~/.bash_history
```

The room emphasizes reading the file directly rather than relying on the numbered `history` output — direct file access works with text-processing tools (`grep`, `awk`, `tail`) and avoids the session-only constraints of the built-in `history` command.

---

## Tools Used

| Command | Purpose |
|---------|---------|
| `whoami` | Confirm which user is running the current shell |
| `ssh user@host` | Establish encrypted remote shell over TCP/22 |
| `ls` | List directory contents — primary orientation tool |
| `cat <filename>` | Read file contents to stdout |
| `cd <path>` | Change working directory |
| `history` | Display command history for the current shell session |
| `cat ~/.bash_history` | Read the persistent bash history file directly |

---

## Takeaways

- **`~/.bash_history` is one of the first files DFIR analysts examine on a compromised Linux host.** It captures every command an interactive user (or attacker) typed in bash. The MITRE technique for clearing it is T1070.003 (Clear Command History). Missing or unusually short bash_history files on a host that should have rich history is itself an indicator of compromise.
- **Reading the file directly vs running `history` matters for analysis.** The `history` command is constrained to the current session and may be truncated or formatted in ways that complicate text processing. `cat ~/.bash_history` gives the raw file, which can be piped into `grep`, `awk`, `sort`, `uniq -c` for proper analysis. Habitual analysts always reach for the file, not the built-in.
- **Attackers know about bash_history and try to defeat it.** Common techniques: `unset HISTFILE` to disable logging mid-session, `export HISTSIZE=0` to prevent recording, prefixing commands with a space (some shells skip space-prefixed commands), running shells with `--norc` or under `nohistory` profiles. Each of these has a detection signature if you know to look for it.
- **SSH is the universal Linux remote access protocol — and a major attacker target.** Brute-force attempts against SSH are constant background noise on any internet-exposed host. The SSH authentication logs (`/var/log/auth.log` on Debian/Ubuntu, `/var/log/secure` on RHEL/CentOS) are critical for IR work — failed authentication patterns reveal scanning, password spraying, and credential stuffing campaigns.
- **The "command not found" → "wrong file" → "correct path" recovery flow is real analyst work.** During incident response, you don't have time to panic over a wrong command. Recognizing the error, running `ls` to re-orient, and trying the correct form is the actual skill being trained. This room pairs the theory with practice that mirrors real CLI work.
- **Weak file permissions enable both confidentiality and integrity attacks at once.** Sec+ tests this concept in scenarios where you have to identify the most likely impact of an over-permissive file. Always remember: the same misconfiguration that lets an attacker read a sensitive file usually also lets them modify it.
- **The CIA triad is the framework for incident classification.** When writing incident reports, structure findings by which property of the triad was violated. This is testable on Sec+ and standard practice in real SOC workflows.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/operatingsystemsecurity)
- [NCSC — Most common passwords list](https://www.ncsc.gov.uk/blog-post/passwords-passwords-everywhere)
- [MITRE ATT&CK — Clear Command History (T1070.003)](https://attack.mitre.org/techniques/T1070/003/)
- [MITRE ATT&CK — SSH (T1021.004 — Remote Services: SSH)](https://attack.mitre.org/techniques/T1021/004/)
- [MITRE ATT&CK — Brute Force: Password Guessing (T1110.001)](https://attack.mitre.org/techniques/T1110/001/)
- Companion: [Linux Command Reference (personal Notion page)](https://www.notion.so/3564b01dd5e881b2b8a4ff84af26a6de)
