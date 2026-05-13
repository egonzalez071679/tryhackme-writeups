# OSI Model — TryHackMe

> **Path:** Pre-Security  
> **Difficulty:** Easy  
> **Category:** Networking  
> **Completed:** 2026  
> **Room Link:** https://tryhackme.com/room/osimodelzi

---

## Summary

Comprehensive walkthrough of the OSI (Open Systems Interconnection) model — the seven-layer framework that standardizes how network communication works. Understanding the OSI model is essential for troubleshooting, packet analysis, and understanding where attacks occur in the network stack.

---

## Key Concepts Learned

- **OSI Model** — A conceptual framework with 7 layers that describes how data travels from an application on one device to an application on another
- **Encapsulation** — The process of each layer adding its own header (and sometimes trailer) to data as it travels down the stack before transmission
- **De-encapsulation** — The reverse process on the receiving end — each layer strips its header as data travels up the stack
- **PDU (Protocol Data Unit)** — The name for data at each layer: data, segment, packet, frame, bits
- **Protocols per layer** — Each layer has specific protocols that operate at that level (e.g. HTTP at Layer 7, TCP at Layer 4, IP at Layer 3)

---

## Notes & Walkthrough

### The 7 Layers — Top to Bottom

| Layer | Name | PDU | Key Protocols | Purpose |
|-------|------|-----|---------------|---------|
| 7 | **Application** | Data | HTTP, HTTPS, FTP, SMTP, DNS | Interface between the user/application and the network |
| 6 | **Presentation** | Data | SSL/TLS, JPEG, ASCII | Translates, encrypts, and compresses data |
| 5 | **Session** | Data | NetBIOS, RPC | Establishes, manages, and terminates sessions between applications |
| 4 | **Transport** | Segment | TCP, UDP | End-to-end communication, flow control, error checking |
| 3 | **Network** | Packet | IP, ICMP, ARP | Logical addressing (IP) and routing between networks |
| 2 | **Data Link** | Frame | Ethernet, MAC, ARP | Physical addressing (MAC) and error detection within a LAN. Combines packets into bytes and bytes into frames |
| 1 | **Physical** | Bits | Ethernet cable, Wi-Fi, fiber | Transmits raw binary bits over a physical medium |

**Memory aid:** "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way" (Physical → Data Link → Network → Transport → Session → Presentation → Application)

### Task 1 — Layer 7: Application

Where users interact with the network. Protocols like HTTP, HTTPS, FTP, DNS, and SMTP live here.

**Security relevance:** Most attacks target Layer 7 — web app attacks (SQLi, XSS), phishing (SMTP), DNS poisoning. WAFs (Web Application Firewalls) operate here.

### Task 2 — Layer 6: Presentation

Handles data formatting, encryption, and compression. TLS/SSL encryption happens here — when you see HTTPS, this layer is doing the encrypting.

**Security relevance:** Certificate validation failures and TLS misconfiguration are Layer 6 issues. Man-in-the-Middle attacks often target this layer to intercept encrypted traffic.

### Task 3 — Layer 5: Session

Manages the lifecycle of a connection — opening, maintaining, and closing sessions between two applications. Also handles authentication at the session level.

**Security relevance:** Session hijacking attacks exploit this layer — stealing a valid session token to impersonate an authenticated user.

### Task 4 — Layer 4: Transport

Provides end-to-end communication between processes. Two main protocols:

| Protocol | Type | Use case |
|----------|------|----------|
| **TCP** | Connection-oriented | Reliable delivery — web browsing, file transfer, email |
| **UDP** | Connectionless-stateless | Fast, no guarantee — video streaming, DNS, VoIP |

TCP uses a **3-way handshake** to establish connections: SYN → SYN-ACK → ACK

**Security relevance:** SYN flood attacks (DoS) exploit the TCP handshake — attackers send many SYN packets but never complete the handshake, exhausting server resources. Port scanning (nmap) operates at this layer.

### Task 5 — Layer 3: Network

Handles logical addressing (IP addresses) and routing between different networks. Routers operate here.

```
IP packet header contains:
- Source IP address
- Destination IP address
- TTL (Time to Live) — decremented at each hop to prevent infinite loops
- Protocol (TCP=6, UDP=17, ICMP=1)
```

**Two types of packets at the Network layer:**

| Packet Type | Purpose | Example Protocols |
|---|---|---|
| **Data packets** | Carry user traffic across the internetwork. Use *routed protocols*. | IP, IPv6 |
| **Route-update packets** | Carry topology information between routers, used to build and maintain routing tables. Use *routing protocols*. | RIP, RIPv2, EIGRP, OSPF |

The naming is easy to confuse but the distinction matters: **routed protocols** (IP) are *what gets routed*; **routing protocols** (OSPF) are *how routers learn where to send things*.

**Security relevance:** IP spoofing, ICMP-based attacks (ping of death, ping sweeps), and traceroute all operate at Layer 3. Firewall rules are typically applied here.

### Task 6 — Layer 2: Data Link

Handles physical addressing (MAC addresses) and reliable transmission within a local network. Switches operate here.

Frames include:
- Source MAC address
- Destination MAC address
- Error detection (CRC checksum)

**The IEEE Data Link layer is split into two sublayers:**

| Sublayer | Job | Responsibilities |
|---|---|---|
| **MAC (Media Access Control)** | Manages *how* devices share the physical medium | Physical addressing (MAC addresses), contention rules (first-come-first-served bandwidth sharing), logical topologies (signal path through physical topology), error notification (not correction), ordered frame delivery, optional flow control |
| **LLC (Logical Link Control)** | Manages *what* gets handed up to Layer 3 | Identifies the Network layer protocol (IP, IPv6, etc.), encapsulation services, flow control and sequencing of control bits |

**How LLC works in practice:** when a frame arrives, the LLC header tells the Data Link layer where to send the payload — for example, "this is an IP packet, hand it up to Layer 3 IP." Without LLC, the Data Link layer wouldn't know what kind of payload it's carrying.

**Logical topology** is worth understanding: it's the *signal path* through the network, separate from how cables are physically run. A network can be physically a star (everyone plugged into a switch) but logically a bus (one shared signal path).

**Security relevance:** ARP spoofing, MAC flooding (CAM table overflow), and VLAN hopping attacks all operate at the MAC sublayer specifically. T1557.002 (ARP Cache Poisoning) lives here. Network forensics using Wireshark captures Layer 2 frames and shows both sublayers' fields.

### Task 7 — Layer 1: Physical

The actual physical transmission — electrical signals on copper cable, light pulses on fiber, radio waves for Wi-Fi. No addressing, just raw bits.

**Security relevance:** Physical layer attacks include cable tapping, hardware keyloggers, and rogue Wi-Fi access points.

### Encapsulation in Action

When you send an HTTP request, data travels down the stack:

```
Application (Layer 7):  [HTTP Data]
Transport   (Layer 4):  [TCP Header | HTTP Data]
Network     (Layer 3):  [IP Header | TCP Header | HTTP Data]
Data Link   (Layer 2):  [MAC Header | IP Header | TCP Header | HTTP Data | CRC]
Physical    (Layer 1):  101010110101010... (binary bits on the wire)
```

On the receiving end, each layer strips its header going back up.

---

## Tools Used

| Tool | Command | Purpose |
|------|---------|---------|
| `wireshark` | GUI | Capture and inspect packets at every OSI layer |
| `tcpdump` | `tcpdump -i eth0` | Command-line packet capture (Layers 2–7) |
| `nmap` | `nmap -sS <target>` | Port scanning operating at Layers 3–4 |

---

## Takeaways

- Every network attack maps to a specific OSI layer — knowing the model tells you where to look for evidence and what controls can stop it
- When analyzing a packet capture in Wireshark, the OSI model is the mental map — Layer 2 frame → Layer 3 packet → Layer 4 segment → Layer 7 payload
- TCP's 3-way handshake appears constantly in network logs — incomplete handshakes at scale indicate port scanning or SYN flood DoS
- Encapsulation explains why Wireshark shows nested headers — peeling each layer reveals the next, exactly like opening nested envelopes
- For IR/DFIR: Layer 7 = what was done (application data), Layer 3/4 = who did it (IP/port), Layer 2 = where on the LAN (MAC)
- Layer 2 sublayer awareness (MAC vs LLC) is what makes ARP spoofing, MAC flooding, and VLAN hopping comprehensible as distinct attack categories
- Layer 3 routed-vs-routing protocol distinction matters for incident scope: BGP/OSPF compromise is a topology-level event, while IP-level attacks affect individual flows

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/osimodelzi)
- [Cloudflare — What is the OSI Model?](https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/)
- [MITRE ATT&CK — Network Sniffing (T1040)](https://attack.mitre.org/techniques/T1040/)
- [MITRE ATT&CK — ARP Cache Poisoning (T1557.002)](https://attack.mitre.org/techniques/T1557/002/)
- Lammle, T. — *CompTIA Network+ Study Guide*, Chapter 2 (OSI Model)
