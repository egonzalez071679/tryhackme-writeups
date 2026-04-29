# Packets & Frames — TryHackMe

> **Path:** Pre-Security  
> **Difficulty:** Easy  
> **Category:** Networking  
> **Completed:** 2026  
> **Room Link:** https://tryhackme.com/room/packetsframes

---

## Summary

Deep dive into how data actually moves across networks at the packet and frame level — what packets and frames are at different OSI layers, how TCP and UDP differ, how the three-way handshake works, and why ports and protocol headers are foundational for nearly every defender alert.

---

## Key Concepts Learned

- **Packet vs Frame** — A packet is a Layer 3 (Network) data unit containing IP headers and payload; a frame is a Layer 2 (Data Link) data unit that encapsulates the packet and adds MAC addressing
- **Encapsulation** — As data moves down the OSI stack, each layer adds its own headers; the reverse process (going up) is decapsulation
- **TCP** — Connection-based protocol that guarantees delivery via the three-way handshake (SYN → SYN/ACK → ACK); reliable but higher overhead
- **UDP** — Stateless protocol with no handshake or delivery guarantee; lower overhead, used where speed matters more than reliability (streaming, voice, DNS)
- **Ports** — Numerical endpoints (0–65535) that identify which application or service a connection belongs to; ports 0–1024 are "common" / well-known
- **TCP/IP model** — Four-layer summarised version of OSI: Application, Transport, Internet, Network Interface

---

## Notes & Walkthrough

### Task 1 — Packets & Frames

Packets and frames are small pieces of data that combine to form a larger message. They live at different OSI layers:

| Unit | OSI Layer | Adds |
|------|-----------|------|
| **Packet** | Layer 3 (Network) | IP header + payload |
| **Frame** | Layer 2 (Data Link) | Encapsulates the packet + MAC addresses |

Splitting messages into small units reduces bottlenecks — large messages sent as one chunk are far more disruptive to a network than the same data broken into many small pieces.

### Task 2 — TCP / IP (The Four Layers)

TCP/IP is essentially a condensed version of the OSI model:

```
+----------------------+
|     Application      |  ← what the user / app sees
+----------------------+
|     Transport        |  ← TCP or UDP
+----------------------+
|     Internet         |  ← IP addressing, routing
+----------------------+
|  Network Interface   |  ← physical / link layer
+----------------------+
```

Information is added to each layer as data traverses it (encapsulation); reversed on the way back up (decapsulation).

### Task 3 — TCP

TCP is **connection-based** — a connection must be established between client and server before any data is sent.

**Trade-offs:**

| Advantages | Disadvantages |
|------------|---------------|
| Guaranteed delivery (acknowledgements) | Higher overhead than UDP |
| Synchronised data transfer | Slower handshake setup |
| Error checking and retransmission | Reserves system resources until closed |

**Key TCP headers:**

| Header | Purpose |
|--------|---------|
| Source / Destination Port | Identifies the application / service on each side |
| Sequence Number | Orders the bytes for reassembly |
| Acknowledgement Number | Confirms receipt of bytes |
| Flags (SYN, ACK, FIN, RST) | Controls connection state |
| Window Size | Flow control — how much data can be in flight |
| Checksum | Integrity check on the segment |

### Task 4 — Three-Way Handshake

The handshake is how TCP establishes a connection:

```
Client                                 Server
  |  ───── SYN ───────────►  |   "I want to connect"
  |  ◄──── SYN/ACK ───────  |   "OK, I'm ready too"
  |  ───── ACK ───────────►  |   "Confirmed, let's talk"
  |  ──── data flows ─────  |
```

| Message | Meaning |
|---------|---------|
| **SYN** | Synchronise — initiates the connection |
| **SYN/ACK** | Acknowledges the SYN and synchronises back |
| **ACK** | Acknowledges the SYN/ACK; connection is now open |

**Closing a connection** uses a similar exchange initiated with a **FIN** packet, also acknowledged by the other side. Because TCP reserves system resources, closing connections promptly is best practice.

### Task 5 — UDP

UDP is **stateless** — no handshake, no acknowledgements, no synchronisation. Used where applications can tolerate loss or where setup overhead would be a problem (video streaming, voice, DNS queries).

| Advantages | Disadvantages |
|------------|---------------|
| Low overhead, fast | No delivery guarantee |
| No connection setup required | No data integrity safeguards |
| Good for real-time applications | Packets can arrive out of order |

UDP packets share some common headers with TCP (Source Port, Destination Port, Length, Checksum) but lack TCP's flags, sequence numbers, and acknowledgement numbers — that's the whole point.

### Task 6 — Ports

Ports are numerical endpoints between **0 and 65535**. Once a connection is established, data sent or received by a device travels through these ports.

**Common ports (0–1024)** — well-known services use standardised ports:

| Port | Protocol | Service |
|------|----------|---------|
| 21 | TCP | FTP |
| 22 | TCP | SSH |
| 25 | TCP | SMTP (mail) |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 3389 | TCP | RDP |

These standards aren't enforced — applications can run on non-standard ports (e.g., a web server on `:8080`). When that happens, clients must explicitly specify the port using a colon (e.g., `http://example.com:8080`).

Full registry: [IANA Service Name and Transport Protocol Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)

---

## Tools Used

| Tool | Command | Purpose |
|------|---------|---------|
| `netstat` | `netstat -an` | List active TCP/UDP connections and listening ports on the local host |
| `ss` | `ss -tuln` | Modern replacement for netstat — list listening sockets |
| `nmap` | `nmap -sS <target>` | TCP SYN scan — detect open ports without completing the handshake |
| `tcpdump` | `tcpdump -i <iface> port 80` | Capture packets for a specific port from the CLI |
| Wireshark | `tcp.flags.syn == 1` | GUI packet analysis; filter by TCP flags to find handshakes |

---

## Takeaways

- **Three-way handshake patterns are bread-and-butter for SOC analysts** — incomplete handshakes (lots of SYN with no SYN/ACK return) are the signature of port scanning; floods of SYN with no ACK completion are SYN flood DDoS attacks
- **Port-based detection is foundational** — nearly every firewall rule, IDS signature, and SIEM correlation starts with "what port was this on?" Knowing common ports cold means reading logs at speed
- **TCP flags reveal intent** — RST floods, FIN scans, and Xmas scans all manipulate flag combinations to evade detection or probe for open ports; recognising these in a packet capture is a Day-1 analyst skill
- **UDP traffic deserves extra suspicion in logs** — because UDP is connectionless and rarely logged as fully as TCP, attackers use it for DNS tunneling, data exfiltration, and C2 beaconing (T1071.001 / T1048.003)
- **Non-standard ports are a hunting opportunity** — services running on unexpected ports (HTTP on :8443, SSH on :2222, RDP on anything but :3389) are worth investigating; legitimate use exists but so does deliberate evasion
- **Encapsulation matters in DFIR** — when correlating across log sources, the same connection appears differently at each layer (MAC at L2, IP at L3, port at L4); knowing what each layer sees is what lets you pivot between firewall logs, EDR data, and PCAPs

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/packetsframes)
- [IANA Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)
- [Cloudflare — What is TCP?](https://www.cloudflare.com/learning/ddos/glossary/tcp-ip/)
- [MITRE ATT&CK — Application Layer Protocol (T1071)](https://attack.mitre.org/techniques/T1071/)
- [MITRE ATT&CK — Exfiltration Over Alternative Protocol (T1048)](https://attack.mitre.org/techniques/T1048/)
