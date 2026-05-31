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
- **ARP (Address Resolution Protocol)** — Maps Layer 3 IP addresses to Layer 2 MAC addresses on the local segment using a request/reply broadcast model; results are stored in a local ARP cache
- **DHCP (Dynamic Host Configuration Protocol)** — Automates IP configuration for devices joining a network using a four-step DORA exchange (Discover, Offer, Request, Acknowledge); the alternative is manual static assignment

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

#### Two identifiers, one device

Every device on a network has two identifiers that operate at different layers of the stack:

- **IP address (Layer 3)** — the logical address used to route traffic across networks
- **MAC address (Layer 2)** — the physical hardware address burned into the NIC, used to deliver frames within a single segment

ARP is the protocol that ties these two identifiers together. It allows a device to associate its MAC address with an IP address on the local network, so traffic addressed to a logical IP can actually be delivered to a physical interface.

#### The ARP cache (the ledger)

Each device maintains a local **ARP cache** — essentially a ledger of IP-to-MAC mappings it has learned. Before sending traffic to another host on the same subnet, a device first checks its cache. If the mapping is already there, no ARP exchange is needed. If not, ARP fires off a request.

```bash
arp -a          # View the ARP cache on your system
```

#### How ARP works — request and reply

ARP uses just two message types to build and maintain those mappings:

1. **ARP Request** — Broadcast to the entire local segment, asking *"What is the MAC address that owns this IP?"* Every device on the segment receives it.
2. **ARP Reply** — Only the device that owns the queried IP responds, sending back its MAC address. All other devices ignore the request.

The requesting device caches the result for future use, so it does not have to ask again until the entry expires.

A typical exchange:

1. Source device broadcasts an ARP Request: *"Who has IP `192.168.1.5`? Tell `192.168.1.1`"*
2. The device that owns `192.168.1.5` sends an ARP Reply: *"I have `192.168.1.5`, my MAC is `aa:bb:cc:dd:ee:ff`"*
3. The source device adds this mapping to its ARP cache for future communication.

#### Why the broadcast model matters

ARP works because every device on the local segment is forced to listen to the broadcast. This is also why ARP only works *within* a broadcast domain — once a router boundary is crossed, ARP requests do not propagate. Communication across networks relies on each segment's own ARP exchange between the host and its default gateway, then between routers, and finally between the destination router and the destination host.

This ties directly back to the encapsulation model: the destination IP in a packet stays the same end-to-end, but the destination MAC is rewritten at every hop based on the local ARP cache of the forwarding device.

#### Security relevance

ARP has no authentication built into the protocol — replies are accepted at face value. This makes it vulnerable to **ARP spoofing / ARP cache poisoning**, where an attacker sends forged ARP replies to associate their own MAC address with a legitimate IP (often the default gateway). Once the victim's cache is poisoned, traffic intended for the gateway is silently redirected through the attacker's machine — a classic Man-in-the-Middle position.

Detection signals worth flagging in a SOC:

- The same IP address mapping to two different MAC addresses in nearby timeframes
- High volumes of unsolicited ARP replies (gratuitous ARP) from a single host
- A host's MAC suddenly appearing as the gateway's MAC in other devices' caches

### Task 5 — DHCP (Dynamic Host Configuration Protocol)

#### Manual vs. automatic IP assignment

IP addresses can be assigned to a device in two ways:

- **Manually (static)** — an administrator physically enters the IP, subnet mask, gateway, and DNS server on the device. Used for servers, printers, network gear, and anything that needs a predictable address.
- **Automatically (dynamic)** — the device gets its configuration from a **DHCP server** the moment it joins the network. This is the default for client devices: laptops, phones, IoT gear, and most workstations.

DHCP is the protocol that powers the automatic path. Without it, every device joining a network would need a human to configure it by hand.

#### The DHCP discovery flow (DORA)

When a device with no static configuration connects to a network, it goes through a four-step exchange known as **DORA**:

1. **Discover** — The client broadcasts a *DHCP Discover* message asking *"Are there any DHCP servers on this network?"* It has no IP yet, so the source address is `0.0.0.0` and the destination is the broadcast address `255.255.255.255`.
2. **Offer** — A DHCP server replies with a *DHCP Offer* containing an available IP address the client could use, plus subnet mask, gateway, DNS servers, and lease duration.
3. **Request** — The client replies with a *DHCP Request*, formally asking for the offered IP. This is also a broadcast, so any other DHCP servers that made offers know their offers were declined.
4. **Acknowledge** — The DHCP server confirms with a *DHCP ACK*. The client may now use the IP for the duration of the lease.

DHCP runs over UDP — server on port 67, client on port 68 — which is why broadcast Discover messages work in the first place: there is no connection to establish before the client even has an IP.

#### Lease renewal

A DHCP lease has a finite lifetime (often 24 hours on home networks, longer in enterprises). Before the lease expires, the client tries to renew with the original server using a unicast Request/ACK pair — no broadcast needed since both sides already know each other.

#### Security relevance

Because DHCP Discover is broadcast and any device can answer, **rogue DHCP servers** are a classic Man-in-the-Middle technique. An attacker plugs in (or spins up a VM on) a DHCP server that responds faster than the legitimate one. Victims accept the rogue offer and receive a malicious gateway, DNS server, or both — silently routing all internet-bound traffic through the attacker.

Detection signals worth flagging in a SOC:

- DHCP offers coming from an unexpected MAC address or interface
- Clients suddenly receiving a non-standard gateway or DNS server
- A spike in DHCP Discover messages (possible DHCP starvation attack to exhaust the legitimate scope)
- Lease assignments from outside the normal DHCP server's IP scope

---

## Tools Used

| Tool | Command | Purpose |
|------|---------|---------|
| `arp` | `arp -a` | View the local ARP cache |
| `ipconfig` / `ip a` | `ip a` | View IP address and subnet assignment |

---

## Takeaways

- Understanding LAN topology helps visualize how traffic flows during an incident — knowing where switches and routers sit explains why some traffic is visible in captures and some isn't
- ARP spoofing is a real-world attack vector — recognizing duplicate IP-to-MAC mappings, unsolicited ARP replies, or sudden gateway MAC changes in logs is a key SOC detection skill
- Rogue DHCP servers are a common initial access / MitM technique — offers from unexpected MACs, non-standard gateways or DNS servers, and unexplained spikes in Discover traffic all warrant investigation
- Subnetting explains why `192.168.1.x` devices can reach each other but need a router to reach `10.0.0.x` — critical for understanding network segmentation in enterprise environments

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/introtolan)
- [Cloudflare — What is ARP?](https://www.cloudflare.com/learning/network-layer/what-is-arp/)
- [MITRE ATT&CK — ARP Cache Poisoning (T1557.002)](https://attack.mitre.org/techniques/T1557/002/)
