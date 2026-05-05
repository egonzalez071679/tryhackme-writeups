# Computer Types — TryHackMe

> **Path:** Pre-Security (SEC0) — Computer Fundamentals  
> **Difficulty:** Easy  
> **Category:** Computing Fundamentals  
> **Completed:** 2026-05-04  
> **Room Link:** https://tryhackme.com/room/computertypes

---

## Summary

Short conceptual room categorizing the different forms a computer can take — from laptops and desktops down to the embedded chips inside everyday objects. Establishes shared vocabulary (workstation vs server, IoT vs embedded) that gets used throughout later rooms when describing target environments and attack surfaces.

---

## Key Concepts Learned

- **Computers come in many physical forms** — but they share the same fundamental architecture (CPU, memory, storage, I/O); the form factor is shaped by purpose, not by being a fundamentally different kind of machine
- **The four classic computer types** — laptop, desktop, workstation, server — differentiated by mobility, sustained performance, reliability requirements, and whether they have a screen and keyboard
- **Computers hide everywhere** — smartphones, tablets, IoT devices, and embedded computers extend the category far beyond what most people picture when they hear "computer"
- **IoT vs embedded — the distinguishing factor is connectivity** — IoT devices report to or take commands from a network; embedded computers may run for years without ever connecting to anything
- **Trade-offs are inherent** — mobility costs sustained performance; reliability costs money; purpose shapes the design choices

---

## Notes & Walkthrough

### The four classic computer types

| Type | Has screen + keyboard | Main purpose |
|------|----------------------|--------------|
| **Laptop** | Yes | Portable everyday computing |
| **Desktop** | Yes | Sustained performance at a fixed location |
| **Workstation** | Yes | Precision and reliability for professional tasks (CAD, video editing, scientific computing) |
| **Server** | No | Providing services to many users over a network |

Workstations look like desktops to a casual observer but are built to higher specs — ECC memory, professional-grade GPUs, certified drivers — for work where reliability and precision matter more than cost.

Servers are designed to run headless (no monitor, no keyboard) in racks, often 24/7, with redundant power supplies and disks.

### Computers in everyday objects

| Type | What it is | Examples |
|------|-----------|----------|
| **Smartphone** | Pocket-sized computer optimized for battery life and connectivity | iPhone, Android phone |
| **Tablet** | Touch-first computer with a larger screen | iPad, drawing tablet |
| **IoT device** | Network-connected device with a single purpose | Smart thermostat, smart doorbell, fitness tracker |
| **Embedded computer** | Computer built into another device, often invisibly | Coffee maker controller, automatic door sensor, lamp dimmer chip |

### IoT vs embedded — the connectivity distinction

Both can be small, single-purpose, and dedicated to one job. The difference is whether they talk to a network:

- **IoT devices** connect — they report data, receive commands, often run firmware that can be updated remotely
- **Embedded computers** typically don't connect — they do their job inside the host machine and most people never know they exist

A smart thermostat is IoT. A microwave oven's timer chip is embedded. The microwave doesn't need WiFi to do its job; the smart thermostat's whole point is being reachable.

### The trade-offs that shape every design

Three principles the room emphasizes:

- **Mobility costs sustained performance.** Smaller, portable computers must sacrifice cooling capacity, battery life, and thermal headroom. A laptop CPU and a desktop CPU with the same model number often perform differently because the laptop throttles to manage heat.
- **Reliability costs money.** Servers and critical systems use redundant components — extra power supplies, RAID disks, ECC memory, dual NICs — to avoid downtime. Each layer of redundancy adds cost.
- **Purpose shapes everything.** You touch a phone constantly. You ask a server for information remotely. An IoT device works quietly without demanding attention. The interaction model determines the form.

The room's closing line captures it: there is no best computer; only the right tool for the job.

---

## Tools Used

This is a conceptual room — no commands, no labs. Reference reading only.

---

## Takeaways

- **The form factor of a target shapes the investigation** — when an analyst is told "this is an IoT device," they should expect different log sources, different update patterns, and different attack vectors than when investigating a server. Vocabulary like this is the entry point to that contextual reading
- **IoT is one of the fastest-growing attack surfaces in real environments** — every connected thermostat, doorbell, and fitness tracker on a corporate network is a potential pivot point. Understanding "IoT means connected" is the first step toward seeing why network segmentation matters for these devices
- **Embedded systems are increasingly relevant for IR/DFIR specializations** — automotive forensics, ICS/SCADA incident response, and IoT firmware analysis are all growing fields built on the recognition that "computer" includes things that don't look like computers
- **Workstation vs server distinction matters for log analysis** — workstations log user-driven activity (logons, file opens, browser sessions); servers log service-driven activity (connections, requests, authentications from many users). Knowing which kind of host generated a log changes how you read it
- **For Sec+ readiness:** the workstation/server/IoT/embedded vocabulary appears in scenario-based questions where you have to identify the most likely attack path or the most appropriate control. Surface-level knowledge is enough; deep memorization isn't

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/computertypes)
- Companion: [Inside a Computer System](./inside-a-computer-system.md), [How Websites Work](./how-websites-work.md)
