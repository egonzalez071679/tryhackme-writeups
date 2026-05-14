# Extending Your Network — TryHackMe

> **Path:** Pre-Security (SEC0) — Network Fundamentals
> **Difficulty:** Easy
> **Category:** Networking / Network Architecture
> **Completed:** 2026-05-12
> **Room Link:** https://tryhackme.com/room/extendingyournetwork

---

## Summary

Closing room of the SEC0 Network Fundamentals module. Covers the technologies that extend a LAN to communicate with the outside world or other networks: **port forwarding** at the router boundary, **firewalls** for traffic control, **VPNs** for connecting separate networks securely, plus a refresher on **routers, switches, and VLANs**. Includes a hands-on **Network Simulator** task that visualizes a TCP packet traversing the network step-by-step — ARP resolution, three-way handshake, the whole flow.

---

## Key Concepts Learned

- **Port forwarding** — mapping an external port on a public IP to an internal IP+port so external traffic can reach internal services
- **Firewall categories** — Stateful (tracks connection state, dynamic) vs Stateless (static ruleset, fast and dumb)
- **Firewall decision factors** — source, destination, port, protocol; firewalls operate at OSI Layers 3 & 4
- **VPN concept** — encrypted tunnel between separate networks creating a logical private network on top of the public internet
- **VPN technologies** — PPP (encryption + auth, non-routable on its own), PPTP (easy to set up, weak encryption), IPSec (strong encryption, harder setup, widely supported)
- **Routers** — Layer 3 devices that connect networks and route packets along the optimal path
- **Switches** — Layer 2 (forward frames by MAC) vs Layer 3 (can also route IP packets)
- **VLANs** — Virtual LANs let one physical switch be split into multiple isolated broadcast domains for security and segmentation

---

## Notes & Walkthrough

### Port forwarding — the basics

Without port forwarding, services hosted inside a LAN are only reachable by devices on that same LAN. A web server at `192.168.1.10:80` serves the other LAN hosts, and that's it — the internet can't see it because `192.168.1.x` addresses aren't routable on the public internet.

Port forwarding configured at the router maps:

```
External: <public_ip>:80   ⇄   Internal: 192.168.1.10:80
```

A request from the internet to `82.62.51.70:80` is rewritten by the router and forwarded to `192.168.1.10:80` on the inside. The response gets translated back on the way out.

**Configured at the router**, not on the server. The server doesn't know external clients exist — it just sees connections coming from the router's inside interface.

### Firewall 101

A firewall is **traffic border control**. It permits or denies packets based on:

- **Source** — where is the traffic coming from?
- **Destination** — where is it going?
- **Port** — what service does it claim to be?
- **Protocol** — TCP, UDP, ICMP?

Firewalls perform packet inspection to evaluate these. The room presents two primary categories:

| Category | How it decides | Trade-off |
|----------|---------------|-----------|
| **Stateful** | Tracks the entire connection. Knows that a returning packet belongs to a session it already approved. If one host's connection is bad, can block the whole device. | More CPU/memory; smarter decisions; modern default. |
| **Stateless** | Static rule set evaluated per-packet. Each packet judged on its own. | Fast and lightweight, good for high-volume traffic like DDoS scrubbing; "dumber" because no context across packets. |

**Note:** Firewalls operate at OSI Layers 3 (IP) and 4 (TCP/UDP). NGFWs add Layer 7 (application) inspection but that's beyond this room's scope.

### VPN Basics — connecting separate networks

A VPN creates an encrypted tunnel between two endpoints across the public internet. Devices on either side appear to be on a single shared private network even though their physical networks are separate.

The room's three-network diagram:

- **Network #1** — Office A's LAN
- **Network #2** — Office B's LAN
- **Network #3 (VPN)** — Logical private network spanning both offices through the tunnel

Members of Network #3 can reach each other directly even though their underlying physical networks (#1 and #2) have no other path between them.

**Benefits highlighted:**

- **Connects networks across geography** — branch offices share resources as if local
- **Privacy via encryption** — traffic in the tunnel is unreadable to ISPs, public WiFi snoopers, or anyone in between
- **Anonymity** — partially. Only as good as the VPN provider's logging practices; a VPN that logs everything is essentially the same as not using one from a privacy standpoint

**VPN technologies the room introduces:**

| Tech | Notes |
|------|-------|
| **PPP** | Provides authentication + encryption using private key + certificate pairs. Cannot route by itself (non-routable). Used as a component, not standalone. |
| **PPTP** | Lets PPP traffic leave a network. Easy to set up, widely supported, **weakly encrypted** — should not be used for sensitive deployments. |
| **IPSec** | Encrypts inside the IP framework. Harder to set up, but strong encryption and broad device support. The modern enterprise default for site-to-site VPNs. |

### LAN Networking Devices — refresher

**Router (Layer 3):** Connects networks and forwards packets between them. Routing decisions based on destination IP and path metrics — shortest path, most reliable, fastest medium. Typically has a config interface (web UI, CLI) where port forwarding rules and firewall ACLs are defined.

**Switch (Layer 2 or Layer 3):**

- **Layer 2 switches** forward frames based on MAC addresses. Each port is its own collision domain. Can't route between IP networks.
- **Layer 3 switches** do everything a Layer 2 switch does PLUS route IP packets between subnets. Common as the core/distribution layer in enterprise networks because hardware-based routing is fast.

**VLAN — Virtual LAN:** Splits one physical switch into multiple isolated broadcast domains. Devices in different VLANs can't talk to each other directly even if they're plugged into the same switch — they only communicate if a router (or Layer 3 switch) explicitly allows it.

The room's example: Sales Department (VLAN 1) and Accounting Department (VLAN 2) both use the same physical switch and both have internet access, but they can't communicate with each other. Security through segmentation.

---

### Practical — Network Simulator

The room deploys an in-browser network simulator with three computers and a router. The challenge: send a TCP packet from `computer1` to `computer3` to reveal a flag, and watch the simulator break down each step of the journey.

#### Exploratory test — loopback first

To get a feel for the simulator's output before attempting the real flow, I sent a test packet from `computer1` to `computer1` itself with `TCP` and data `hello`. Same-host loopback test.

What the Network Log showed:

```
HANDSHAKE: Starting TCP/IP Handshake between computer1 and computer1
HANDSHAKE: Sending SYN Packet from computer1 to computer1
ARP REQUEST:  Who has computer1 tell computer1
ARP RESPONSE: Hey computer1, I am computer1
HANDSHAKE:    computer1 received SYN Packet
... (continued)
```

Useful observations from this trivial case:

- The simulator does an **ARP exchange even for same-host traffic** — it's modeling the full process, not optimizing it away
- The **TCP three-way handshake initiates with a SYN** before any data flows
- ARP request is **broadcast** ("Who has X tell Y"); ARP response is **unicast** back to the requester

This confirmed the textbook flow before I attempted the real challenge.

#### Challenge — `computer1` → `computer3`

Configured the simulator:

- **From:** computer1
- **To:** computer3
- **Packet Type:** TCP
- **Data:** *(any text — the simulator passes the data along; the flag comes from the response generated when the packet successfully reaches computer3)*

The Network Log walked through the full cross-network flow:

1. **TCP/IP Handshake initiation** — computer1 begins the three-way handshake by preparing a SYN packet for computer3
2. **ARP resolution for the next hop** — computer1 doesn't know computer3's MAC, and computer3 isn't on the same subnet, so computer1 ARPs for its **default gateway** (the router). The router answers with its MAC.
3. **SYN packet sent at Layer 2** — the frame's destination MAC is the router's; the destination IP in the packet is still computer3's
4. **Router receives the frame** — it strips the Layer 2 frame header and looks at the destination IP at Layer 3
5. **Router does ARP on its outbound interface** — asking which MAC owns computer3's IP on that segment
6. **Router forwards the packet** — re-encapsulates it in a new Layer 2 frame addressed to computer3's MAC
7. **computer3 receives the SYN** — completes the three-way handshake by responding with SYN-ACK back along the same path in reverse
8. **computer1 receives SYN-ACK** and sends final ACK — connection established
9. **Data is transmitted** — the TCP session carries the payload
10. **Flag revealed** — the simulator outputs the flag indicating a successful end-to-end TCP session

**Flag:** `THM{YOU'VE_GOT_DATA}`

The point of the exercise: see that every cross-network TCP connection involves both **Layer 2 mechanics (ARP, MAC-addressed frames)** AND **Layer 3 mechanics (IP routing through the gateway)** — and that ARP happens twice (once on each LAN segment) when the destination is on a different network than the source.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Network Simulator** (browser-based, deployed by the room) | Visualize packet traversal step-by-step |
| **Chrome / Firefox** | Required browser for the simulator |
| Conceptual reference: OSI Model, ARP, TCP three-way handshake | All from earlier SEC0 rooms |

---

## Takeaways

- **The Network Simulator makes the OSI Model concrete.** Every concept from the OSI Model and Packets & Frames rooms shows up here as actual log lines: ARP requests (Layer 2 + 3 boundary), SYN/SYN-ACK/ACK (Layer 4), frame re-encapsulation at the router (Layer 2 changes per hop, Layer 3 stays the same). Watching it happen step-by-step is one of the best mental models a beginning analyst can build.
- **Cross-network traffic involves ARP twice.** Source ARPs for the gateway; router ARPs for the destination on the next segment. When investigating lateral movement, recognizing this pattern in packet captures tells you when traffic crossed a routed boundary versus stayed local on the same VLAN.
- **Port forwarding is a known attacker target.** Every port forward on a perimeter router widens the attack surface. SOC teams audit port-forwarding rules and watch for unauthorized changes — adding a port-forward rule is a common post-compromise persistence action on home routers in IoT/SOHO incidents.
- **Stateful firewall logs are the central session log source in IR work.** Every connection through the firewall — allowed or denied — produces a log entry with src IP, dst IP, src port, dst port, protocol, action, and (often) bytes/packets in each direction. When triaging an alert, "what does the firewall log show for this IP pair?" is usually the first question.
- **Stateless firewalls still have a role in DDoS scrubbing and high-throughput perimeters** because their per-packet decision is fast. Modern environments often combine: stateless filtering at the edge for volume, stateful inspection deeper in the stack for context-aware decisions.
- **VPN compromise is one of the top initial-access vectors for modern ransomware.** Per Mandiant and CrowdStrike threat reports, compromised VPN credentials (often from infostealer logs) appear repeatedly as the entry point. VPN authentication logs deserve the same scrutiny as domain controller auth logs — impossible-travel detection, unusual session durations, off-hours logins.
- **PPTP should not be deployed.** Cryptographically broken since 2012. Seeing PPTP in a network inventory is itself a finding. Modern deployments use IPSec (site-to-site, traditional), or WireGuard / OpenVPN / SSL VPN clients (remote-access, modern).
- **VLAN segmentation is a security boundary, but not unbreakable.** VLAN hopping attacks (double-tagging, switch spoofing — MITRE T1557.002 territory in adjacent techniques) let an attacker traverse VLANs that should be isolated. Properly configured switches disable Dynamic Trunking Protocol (DTP), explicitly set access ports, and never use the native VLAN for sensitive traffic.
- **Layer 3 switches blur the router/switch line.** Most enterprise inter-VLAN routing happens at multilayer switches, not standalone routers — hardware-based, fast, and built into the core distribution layer. Knowing this matters when reading network diagrams: what looks like a switch may be doing routing.

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/extendingyournetwork)
- [RFC 1918 — Address Allocation for Private Internets](https://datatracker.ietf.org/doc/html/rfc1918)
- [RFC 2637 — PPTP](https://datatracker.ietf.org/doc/html/rfc2637) (historical / not for new deployments)
- [RFC 4301 — Security Architecture for the Internet Protocol (IPSec)](https://datatracker.ietf.org/doc/html/rfc4301)
- [MITRE ATT&CK — Network Service Discovery (T1046)](https://attack.mitre.org/techniques/T1046/) — relevant to port forwarding exposure
- [MITRE ATT&CK — External Remote Services (T1133)](https://attack.mitre.org/techniques/T1133/) — VPN as initial access vector
- [MITRE ATT&CK — Adversary-in-the-Middle: ARP Cache Poisoning (T1557.002)](https://attack.mitre.org/techniques/T1557/002/) — VLAN/Layer-2 attack surface
- Companion: [Lammle Ch.5 Networking Devices — Detailed Study Resume](https://www.notion.so/34e4b01dd5e8819681b7cf9060958ad9) (in Notion)
