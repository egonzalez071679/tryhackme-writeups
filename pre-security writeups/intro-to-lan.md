# Intro to LAN — TryHackMe

> **Path:** Pre-Security  
> **Difficulty:** Easy  
> **Category:** Networking  
> **Completed:** 2026  
> **Room Link:** https://tryhackme.com/room/introtolan

---

## Summary

Deep dive into Local Area Networks — how devices are physically and logically connected, how they identify each other, and how network services like ARP and DHCP operate behind the scenes. Builds the networking vocabulary needed to understand traffic analysis and incident investigations.

---

## Key Concepts Learned

- **LAN topology** — The physical or logical layout of a network; Star, Bus, and Ring are the three main types
- **Switch** — A network device that connects multiple devices within a LAN and forwards traffic only to the intended recipient using MAC addresses
- **Router** — Connects different networks together and directs traffic between them using IP addresses
- **Subnetting** — Dividing a network into smaller sub-networks to improve performance, security, and management
- **ARP (Address Resolution Protocol)** — Resolves IP addresses to MAC addresses within a local network; operates at Layer 2
- **DHCP (Dynamic Host Configuration Protocol)** — Automatically assigns IP addresses, subnet masks, gateways, and DNS servers to devices joining a network

---

## Notes & Walkthrough

### Task 1 — LAN Topologies

The three main topologies and their trade-offs:

| Topology | How it works | Strength | Weakness |
|----------|-------------|----------|----------|
| **Star** | All devices connect to a central switch/hub | Easy to manage, fault-tolerant | Single point of failure at the switch |
| **Bus** | All devices share a single cable backbone | Simple, cheap | One failure affects all devices |
| **Ring** | Devices connected in a loop | Predictable performance | One break disrupts the entire network |

Star is the dominant topology in modern networks — almost every office and home network uses it.

### Task 2 — Switches and Routers

**Switches** operate at Layer 2 (Data Link). They:
- Maintain a MAC address table to track which device is on which port
- Forward frames only to the destination device — not everyone on the network
- Reduce unnecessary traffic (unlike older hubs which broadcast to all)

**Routers** operate at Layer 3 (Network). They:
- Connect different networks (e.g. your LAN to the internet)
- Use routing tables to determine the best path for traffic
- Assign a default gateway that devices use to reach outside their subnet

### Task 3 — Subnetting

Subnetting splits a large network into smaller, more manageable segments.

```
Example:
Network:   192.168.1.0/24
Subnet mask: 255.255.255.0
Usable hosts: 192.168.1.1 — 192.168.1.254
Broadcast:   192.168.1.255
```

Key reasons to subnet:
- **Security** — isolate sensitive systems (e.g. finance VLAN from general staff)
- **Performance** — reduce broadcast traffic
- **Management** — easier to apply firewall rules per subnet

### Task 4 — ARP (Address Resolution Protocol)

ARP bridges the gap between Layer 3 (IP) and Layer 2 (MAC). When a device wants to communicate with another IP on the same network:

1. Device sends an **ARP Request** broadcast: "Who has IP `192.168.1.5`? Tell `192.168.1.1`"
2. The device with that IP replies with an **ARP Reply**: "I have `192.168.1.5`, my MAC is `aa:bb:cc:dd:ee:ff`"
3. The requester caches this in its **ARP table** for future use

```bash
arp -a          # View the ARP cache on your system
```

**Security relevance:** ARP has no authentication — this makes it vulnerable to **ARP spoofing/poisoning**, where an attacker sends fake ARP replies to redirect traffic through their machine (Man-in-the-Middle attack).

### Task 5 — DHCP (Dynamic Host Configuration Protocol)

DHCP automates IP address assignment using a four-step process (DORA):

1. **Discover** — Device broadcasts: "Is there a DHCP server?"
2. **Offer** — DHCP server responds: "Here is an available IP address"
3. **Request** — Device accepts: "I'll take that IP"
4. **Acknowledge** — Server confirms: "It's yours for the lease duration"

**Security relevance:** **Rogue DHCP servers** are a common attack — an attacker sets up a fake DHCP server to hand out a malicious gateway or DNS server, redirecting all victim traffic.

---

## Tools Used

| Tool | Command | Purpose |
|------|---------|---------|
| `arp` | `arp -a` | View the local ARP cache |
| `ipconfig` / `ip a` | `ip a` | View IP address and subnet assignment |

---

## Takeaways

- Understanding LAN topology helps visualize how traffic flows during an incident — knowing where switches and routers sit explains why some traffic is visible in captures and some isn't
- ARP spoofing is a real-world attack vector — recognizing unusual ARP traffic in logs is a key SOC detection skill
- Rogue DHCP servers are a common initial access / MitM technique — suspicious DHCP lease assignments in logs warrant investigation
- Subnetting explains why `192.168.1.x` devices can reach each other but need a router to reach `10.0.0.x` — critical for understanding network segmentation in enterprise environments

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/introtolan)
- [Cloudflare — What is ARP?](https://www.cloudflare.com/learning/network-layer/what-is-arp/)
- [MITRE ATT&CK — ARP Cache Poisoning (T1557.002)](https://attack.mitre.org/techniques/T1557/002/)
