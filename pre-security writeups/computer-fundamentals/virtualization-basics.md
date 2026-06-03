# Virtualization Basics — TryHackMe

> **Path:** Pre-Security (SEC0) — Computers and OS Security  
> **Difficulty:** Easy  
> **Category:** Virtualization / Infrastructure Fundamentals  
> **Completed:** 2026-05-12  
> **Room Link:** https://tryhackme.com/room/virtualizationbasics

---

## Summary

Foundational room on virtualization — the technology that lets one physical computer act like many separate computers. Covers the hypervisor (the software that creates and manages virtual machines), Type 1 vs Type 2 hypervisors, the distinction between VMs and containers, and Docker. Includes a hands-on lab using a simulated hypervisor management console to fix a VM in an error state, create a new VM, and analyze physical host capacity. For a security analyst, virtualization is foundational — it's the basis of malware analysis sandboxes, home labs, and nearly all cloud infrastructure.

---

## Key Concepts Learned

- **Virtualization** — enables a single physical computer to act like multiple separate computers
- **Hypervisor** — the manager software that creates and runs virtual machines; divides physical hardware into isolated virtual ones
- **Type 1 hypervisor** — runs directly on physical hardware (bare metal); fast, efficient, ideal for servers and data centers
- **Type 2 hypervisor** — runs within an existing OS (hosted); easier to install, ideal for learning, testing, home labs
- **Virtual Machine (VM)** — a full virtual computer with its own virtual CPU, RAM, storage, network, and OS; fully isolated from other VMs
- **Container** — a lightweight isolated environment running a single app; shares the host's kernel instead of bringing a whole OS
- **Container image** — a pre-packaged template/recipe used to create containers
- **Docker** — the most common platform for building and running containers
- **The hierarchy** — Physical Server → Hypervisor → VMs → (Containers can run inside a VM)

---

## Notes & Walkthrough

### Why virtualization exists

Before virtualization, each application often ran on its own physical server. This was wasteful — a server running one app might use 10% of its CPU while the rest sat idle, yet you still paid for the whole machine, its power, and its rack space.

Virtualization introduced a better question: *what if multiple applications could share the same physical server safely?* The answer was the hypervisor — a layer that acts as a referee between virtual machines, letting each behave like an independent physical computer while sharing the same underlying hardware.

Benefits the room highlights: cost savings, better resource usage, safe testing for cyber security, faster deployment, flexibility, portability, scalability, and centralized management.

### The hypervisor

A hypervisor is the core technology behind virtualization. It:

- Divides a physical computer into multiple virtual ones
- Gives each VM its own share of CPU, memory, and storage
- Keeps everything isolated and safe
- Manages the VM lifecycle — start, stop, pause, clone, delete

There are two types:

| | **Type 1 (Bare Metal)** | **Type 2 (Hosted)** |
|---|---|---|
| **Runs on** | Directly on physical hardware | Inside an existing OS |
| **Speed** | Faster, more efficient | Slightly slower (OS overhead) |
| **Best for** | Servers, data centers, production | Learning, testing, home labs |
| **Examples** | VMware ESXi, Microsoft Hyper-V, Proxmox, Xen | VirtualBox, VMware Workstation, VMware Fusion |

**Use case mapping from the room:**

| Use Case | Best Hypervisor Type |
|----------|---------------------|
| Test malicious files | Type 2 |
| Production server | Type 1 |
| Database server | Type 1 |
| Software testing | Type 2 |
| Kali Linux (learning) | Type 2 |
| Data center | Type 1 |

> **Note on malware testing:** when using virtualization to test malicious files, care must be taken that the host doesn't get infected by malware in the guest. Approaches include using different OSes for guest vs host, and isolating the guest so it can't communicate with the host. (This is the conceptual seed of building a proper malware analysis lab.)

### Virtual Machines — "the full apartment"

A VM is a complete virtual computer. Even though it's virtual, it behaves like a real machine:

- Has its own virtual CPU, RAM, storage, and network
- Can run any OS (Windows, Linux, macOS)
- Is completely isolated — if one VM breaks, the others keep running

You deploy VMs on your own computer with Type 2 hypervisors like Oracle VirtualBox or VMware Workstation. Common analyst use cases:

- Run Kali Linux without buying a separate machine
- Test whether a file is malicious in an isolated environment that protects your main computer

### Containers — "the rooms inside the apartment"

A container is a lightweight, isolated environment that runs a single application plus everything it needs. Instead of bringing a whole separate OS, a container borrows the core of the existing system by running on the **kernel** — the part of the OS that talks to hardware and manages resources.

Because containers share this kernel:

- They start almost instantly and use fewer resources than full VMs
- They must match the host system's type — you can't run a Windows container on a Linux machine
- They package the app and its dependencies (libraries, tools, versions)
- They remain isolated from each other — a misbehaving container doesn't affect the others
- They run consistently on any machine — ideal for development, testing, and scalable deployment

**Docker** is the most common platform for building, deploying, and running containers.

### The relationship (VMs vs containers)

```
┌─────────────────────────────────────────────┐
│              Physical Server                  │
│  ┌─────────────────────────────────────────┐ │
│  │             Hypervisor                    │ │
│  │  ┌──────────────┐  ┌──────────────────┐  │ │
│  │  │ Virtual      │  │ Virtual Machine B │  │ │
│  │  │ Machine A    │  │ ┌─────┐ ┌───────┐ │  │ │
│  │  │              │  │ │Cont.│ │Cont.  │ │  │ │
│  │  │              │  │ │  A  │ │  B    │ │  │ │
│  │  │              │  │ └─────┘ └───────┘ │  │ │
│  │  └──────────────┘  └──────────────────┘  │ │
│  └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

VMs give the "full apartment" — maximum separation and flexibility, each with its own OS. Containers are lightweight "rooms" — ideal for fast, scalable applications, sharing the host kernel.

---

### Practical — Managing Virtual Machines

The lab presents a simulated hypervisor management console with three sections: **Summary** (environment overview), **Lab Machines** (per-VM details and actions), and **Hosts** (physical server usage/performance).

#### Task A — Fix the Mail-SERVER error

The `Mail-SERVER` VM had entered an **Error** state. Fixed by clicking the restart (blue square) button to rerun it. After the restart, it returned to Running — bringing the total running VMs to 8.

#### Task B — Create the Marketing-VM

Created a new VM to host the marketing team's website via the **+ Create VM** button, with:

- **Name:** Marketing-VM
- **CPU Cores:** 4
- **Memory (GB):** 8
- **Disk Size (GB):** 100

The new VM appeared in the list (initially Stopped, on host HV-PROD-01, with a DHCP-assigned address).

#### Task C — Analyze hardware usage (Hosts section)

Reading the Host Monitoring panel:

| Host | CPU | Memory | Storage | VMs | State |
|------|-----|--------|---------|-----|-------|
| **HV-PROD-01** | 45% | 68% | 72% | 3 | Connected — has capacity for more VMs |
| **HV-PROD-02** | 98% | 90% | 95% | 8 | Connected — **near 100% capacity, report this!** |
| **HV-BACKUP-01** | 0% | 0% | 30% | 0 | **Disconnected** — not running |

Findings: HV-PROD-01 can host more VMs; HV-PROD-02 is critically overloaded and needs escalation; HV-BACKUP-01 is offline.

#### Lab answers

| Question | Answer | Reasoning |
|----------|--------|-----------|
| Q1 — VM running longest? | **Monitoring-SYS** | Uptime 3 weeks, 2 days — longest in the list |
| Q2 — VM using most memory? | **DB-Cluster-01** | 32 GB — highest memory allocation |
| Q3 — VMs running after fixing Mail-SERVER? | **8** | Mail-SERVER restart brought the running count to 8 |
| Q4 — Physical machine hosting most VMs? | **HV-PROD-02** | Hosts 8 VMs — more than any other host |

**Task answers:** virtualization lets multiple applications share a *physical server*; the software managing each VM's resources is the *hypervisor*; a study lab on your own machine uses a *Type 2* hypervisor; hosting multiple small apps in one VM is best done with *containers*.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Hypervisor management console** (simulated) | View VM states, restart failed VMs, create VMs, monitor host capacity |
| Conceptual: VirtualBox / VMware Workstation | Type 2 hypervisors for home labs |
| Conceptual: VMware ESXi / Hyper-V / Proxmox | Type 1 hypervisors for production |
| Conceptual: Docker | Container platform |

---

## Takeaways

- **The biggest thing I took from this room: virtualization is why you can study security safely.** Running Kali in a VM, or testing a suspicious file inside an isolated guest, only works because the hypervisor keeps the guest separated from my real machine. The room made the point directly — if you're testing malicious files, isolate the guest so it can't reach the host, and consider using a different OS for each. That's the rule I'll follow when I eventually set up my own analysis lab.
- **Type 2 hypervisor = my home lab.** This is the concrete, practical takeaway. VirtualBox or VMware Workstation on my own machine is a Type 2 hypervisor, and that's exactly what I'll use to run practice VMs. Type 1 (bare metal) is the production/data-center side I don't need yet, but it's good to know the distinction.
- **VMs vs containers finally clicked.** A VM is a whole computer with its own OS (heavier, fully isolated); a container shares the host's kernel and just packages one app (lighter, starts instantly, but must match the host's OS type). The "apartment vs rooms" analogy is what made it stick.
- **Reading the host monitoring panel felt like a real triage task.** Looking at three hosts and immediately spotting that HV-PROD-02 was at 98% CPU / 90% memory / 95% storage — and that it needed to be flagged to a manager — is the kind of "notice the thing that's wrong" skill I'm trying to build. Same instinct applies to noticing a machine pinned at 100% for no good reason.
- **Fixing the errored Mail-SERVER was a small but useful reminder** that the first step when something's in an error state is often just to check it and restart it, then confirm the result (8 VMs running afterward). Verifying the outcome, not just taking the action, is the part worth keeping.

> **Where this leads later (not something I did in this room):** virtualization underpins a couple of things I'll meet further into the roadmap — malware analysis sandboxes (snapshot, detonate, revert) and cloud forensics, since cloud servers like the EC2 instance from the Windows CLI room are just VMs on someone else's hypervisor. There's also an attacker side (VM/container escape, sandbox-aware malware) that I'll learn properly when I get to SAL1/CDSA. Noting it here as a pointer, not as something this room covered.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/virtualizationbasics)
- [Oracle VirtualBox — documentation](https://www.virtualbox.org/wiki/Documentation) (Type 2 hypervisor for home labs)
- [Docker — getting started](https://docs.docker.com/get-started/) (containers)
- Companion writeup in this repo: Inside a Computer System (the physical hardware that virtualization abstracts)
