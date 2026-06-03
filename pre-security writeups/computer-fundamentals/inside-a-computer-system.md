# Inside a Computer System — TryHackMe

> **Path:** Pre-Security (SEC0) — How Computers Work  
> **Difficulty:** Easy  
> **Category:** Computer Hardware Fundamentals  
> **Completed:** 2026-05-12  
> **Room Link:** https://tryhackme.com/room/insideacomputersystem

---

## Summary

Foundational room introducing the physical building blocks of a computer system and the boot sequence that turns a powered-off machine into a running operating system. Covers the major internal components — motherboard, CPU, RAM, storage, network adapter, PSU, GPU, and I/O — and walks through the five-step boot process from power button press to bootloader execution. Pairs with the Computer Types room (form factors) to complete the hardware vocabulary side of SEC0.

---

## Key Concepts Learned

- **Motherboard** — the backbone that connects every other component; the skeleton and nervous system of the machine
- **CPU (Central Processing Unit)** — the brain; executes instructions continuously; connects via CPU socket
- **RAM (Random Access Memory)** — fast volatile short-term memory; contents lost on power loss; modern modules use DDR5 / DDR6
- **Storage (SSD / HDD)** — long-term persistent data; survives power cycles
- **Network adapter** — connects the system to other computers; covers wired Ethernet and wireless NICs
- **PSU (Power Supply Unit)** — converts wall AC into the DC voltages internal components need; connects via main motherboard connector + Molex
- **GPU (Graphics card)** — handles visual processing; connects via PCI Express
- **I/O (Input / Output)** — peripherals and their connectors (USB, HDMI, DisplayPort)
- **UEFI / BIOS** — firmware that initializes hardware at power-on; UEFI has mostly replaced legacy BIOS in modern systems
- **POST (Power-On Self Test)** — firmware routine that verifies each required component is present and functioning before handing off
- **Bootloader** — small program on the selected boot device that transfers the operating system from storage into RAM and yields control

---

## Notes & Walkthrough

### The building blocks

Every computer — desktop, laptop, server, even most embedded systems — uses the same fundamental components. The room's analogy maps them to body parts:

| Component | Analogy | Real role |
|-----------|---------|-----------|
| **Motherboard** | Skeleton + nervous system | Physical platform connecting all other components; provides communication buses between them |
| **CPU** | Brain | Executes program instructions; the central decision-maker |
| **RAM** | Short-term memory | Fast working memory for data the CPU needs right now |
| **Storage (SSD/HDD)** | Long-term memory | Persistent data that survives power loss |
| **PSU** | Heart + lungs | Pumps power to every component from the wall outlet |
| **GPU** | Visual cortex | Processes graphics output to the monitor |
| **Network adapter** | Voice | Communicates with other systems |
| **I/O devices** | Hands + ears + eyes | Human-computer interaction surface |

The room mostly establishes vocabulary at this level — it's not a deep-dive into computer architecture. The point is that every machine an analyst will ever investigate has these same parts, even if the form factor is different (Computer Types covers that distinction).

### Critical distinction — volatile vs persistent

The most analytically important point in this room is the **RAM vs storage** distinction:

| Memory type | Speed | Persistence | What survives power loss |
|-------------|-------|-------------|--------------------------|
| **RAM (volatile)** | Very fast | Lost on power-off | Nothing — contents erased |
| **Storage (persistent)** | Slower | Survives power cycles | Everything written to disk |

Modern RAM uses DDR (Double Data Rate) standards — DDR4, DDR5, and DDR6 in current consumer hardware. The room emphasizes the volatility property: **when the power goes off, everything in RAM is gone.** This single fact drives the entire field of memory forensics — the practice of capturing and analyzing RAM contents before power loss.

### Connector and slot reference

The room introduces these physical connection types:

- **CPU socket** — CPU mounts directly on the motherboard
- **PCI Express slots** — high-bandwidth slots for GPUs, NVMe drives, network cards
- **DIMM slots** — for RAM modules
- **SATA / NVMe connections** — for storage devices
- **PSU connectors** — main motherboard (24-pin ATX), CPU power (4/8-pin), Molex (legacy), PCIe power
- **External I/O** — USB (A, C, mini, micro), HDMI, DisplayPort, audio jacks, Ethernet RJ45

These aren't memorize-for-the-exam details — they're the vocabulary you'll need when reading hardware specifications, building or troubleshooting a workstation, or describing systems in incident reports.

### The boot process — 5 steps

This is the most useful technical content in the room. When a computer transitions from "off" to "operating system running," it walks through these steps:

```
Press Power Button → Firmware Starts → POST → Select Boot Device → Initiate Bootloader
```

#### Step 1 — Press the Power Button

The power button sends a signal to the PSU authorizing it to begin delivering power. The PSU then distributes the appropriate voltages to the motherboard, CPU, RAM, storage, and other components via their respective connectors.

#### Step 2 — Firmware starts

The motherboard's **firmware** (a small program stored in non-volatile flash memory on the motherboard itself) begins running. The modern firmware is called **UEFI** (Unified Extensible Firmware Interface). Legacy systems used **BIOS** (Basic Input/Output System) — same conceptual role, mostly replaced by UEFI in machines built after roughly 2010.

UEFI is responsible for orchestrating the rest of the startup sequence. Until it hands control to the operating system, it IS the running software on the machine.

#### Step 3 — Power-On Self Test (POST)

UEFI runs **POST**, a verification routine that checks each required component is present and functioning:

- Is the CPU responding?
- Is there detectable RAM?
- Are storage devices visible?
- Are critical peripherals attached?

If POST fails, the system typically signals the error with beep codes (legacy BIOS), on-screen messages (UEFI), or motherboard LED indicators. A passing POST means the hardware is healthy enough to attempt to boot an operating system.

#### Step 4 — Select Boot Device

UEFI maintains an **ordered list of boot devices** — typically: NVMe drive → SATA SSD → USB drive → network boot (PXE) → CD/DVD. UEFI walks this list looking for a device with a valid bootable partition.

The boot order is configurable in the UEFI settings menu, which the user can usually access by pressing F2, F12, DEL, or a similar key during startup.

#### Step 5 — Initiate Bootloader

Once a bootable device is found, UEFI executes its **bootloader** — a small program that knows how to load the operating system from that device into RAM. Common bootloaders:

- **GRUB** — most Linux distributions
- **Windows Boot Manager** — Windows 10/11
- **systemd-boot** — modern Linux alternative to GRUB

The bootloader transfers the operating system kernel from storage into RAM, then jumps execution to the kernel's entry point. At that moment, UEFI yields control to the OS, and the boot sequence is complete.

The screen flickers, the OS logo appears, drivers load, services start, and eventually the login screen appears.

---

## Tools Used

This is a conceptual room with no hands-on lab in the SAL sense. Tools listed are what's used to inspect these components on a real system after the room:

| Tool | Purpose |
|------|---------|
| **`lscpu` / `cat /proc/cpuinfo`** (Linux) | Identify CPU model, cores, features |
| **`free -h`** (Linux) | Show RAM usage |
| **`lsblk` / `fdisk -l`** (Linux) | List storage devices and partitions |
| **`dmidecode`** (Linux) | Read motherboard, BIOS/UEFI, and hardware details from SMBIOS |
| **Task Manager → Performance tab** (Windows) | View CPU, RAM, GPU, network utilization |
| **`systeminfo`** (Windows) | OS and hardware summary |
| **UEFI settings menu** | Inspect/modify boot order, hardware initialization parameters |

---

## Takeaways

- **RAM is volatile — this is the entire foundation of memory forensics.** Every running process, every loaded DLL, every active network connection, every decrypted key, every clipboard content, every typed password sits in RAM at some point. Capturing a memory image before the system loses power (with tools like FTK Imager, DumpIt, or LiME on Linux) preserves evidence that disappears the moment the machine powers off. Volatility Framework and MemProcFS are the standard analyst tools for parsing memory images. This single property of RAM justifies an entire DFIR specialization.
- **UEFI firmware is an attacker persistence target.** UEFI bootkits (MITRE T1542.003 — Pre-OS Boot: Bootkit) modify firmware to load before the operating system, achieving persistence that survives OS reinstallation. Known UEFI implants include LoJax (first publicly known UEFI rootkit, 2018), MosaicRegressor, and BlackLotus. Detection requires firmware-level analysis tools like CHIPSEC; ordinary OS-level antivirus cannot see these implants. Knowing that UEFI exists and is modifiable is the entry point to understanding this threat class.
- **Boot order modifications are a common physical-access attack.** Changing the UEFI boot order to prioritize an attacker-controlled USB drive lets the attacker boot a live Linux environment with full disk access, bypassing OS-level authentication. Defenses: UEFI password protection, Secure Boot enabled, full disk encryption (BitLocker / LUKS), and physical security of the device. Sec+ Domain 3 (Architecture) tests recognition of these defenses.
- **POST failures are sometimes IR-relevant.** A machine that suddenly fails POST after working fine may have suffered hardware tampering (rogue device added, component damage), failed components, or in rare cases firmware corruption from a malicious update. Most POST failures are mundane hardware issues — but understanding what POST tests helps distinguish hardware from software causes during incident scoping.
- **PCIe is an attack surface, not just a slot.** PCIe devices have Direct Memory Access (DMA) capability, meaning they can read and write system RAM without CPU mediation. Malicious PCIe devices (or compromised Thunderbolt accessories, which expose PCIe over an external cable) can read encryption keys directly out of RAM. Modern defenses include IOMMU/VT-d configuration that blocks DMA from untrusted devices. This is the kind of physical-access attack security-conscious organizations defend against on executive laptops.
- **Network adapter MAC addresses are forensically significant.** Every NIC has a hardware-burned MAC address that uniquely identifies the physical interface. SOC analysts cross-reference MAC addresses against switch CAM tables to localize a compromised device to a specific physical port. The first 6 hex digits (the OUI) identify the manufacturer — a "Dell" OUI appearing on traffic that claims to be a printer is a red flag.
- **Storage media survives most things — including attempts to delete evidence.** SSDs and HDDs retain data after deletion (the file system removes pointers but not the underlying bytes), after quick format (only metadata cleared), and often after full format (still recoverable with proper tools). The DFIR discipline of disk forensics exists because this is true. Knowing storage is persistent is the conceptual foundation for understanding why "I deleted it" rarely means it's gone.
- **GPUs are not just for graphics.** Modern GPUs are massively parallel compute devices used for password cracking (hashcat with GPU acceleration is orders of magnitude faster than CPU), cryptocurrency mining (a common cryptojacking infection symptom), and machine learning workloads. When investigating suspicious resource usage on a host, sustained 100% GPU utilization on a system that shouldn't be using GPU is worth flagging.
- **BIOS/UEFI settings sometimes leak forensic data.** Stored MAC addresses, asset tag fields, custom boot orders, and TPM configurations in UEFI settings can corroborate other IR findings or reveal tampering. Some organizations use UEFI configuration auditing as part of their endpoint compliance posture.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/insideacomputersystem)
- [MITRE ATT&CK — Pre-OS Boot: Bootkit (T1542.003)](https://attack.mitre.org/techniques/T1542/003/)
- [MITRE ATT&CK — Pre-OS Boot: System Firmware (T1542.001)](https://attack.mitre.org/techniques/T1542/001/)
- [MITRE ATT&CK — Forced Authentication (T1187)](https://attack.mitre.org/techniques/T1187/) — DMA-based credential capture context
- [UEFI Forum — Official UEFI Specification](https://uefi.org/specifications) — for the technically curious
- [CHIPSEC — Firmware Security Assessment Framework](https://github.com/chipsec/chipsec) — analyst tool for UEFI inspection
- [Volatility Framework](https://www.volatilityfoundation.org/) — RAM forensics; ties directly to the volatility property of RAM described in this room
- Companion writeup in this repo: Computer Types (form factors complement the components covered here)
