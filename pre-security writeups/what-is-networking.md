# What is Networking? — TryHackMe

> **Path:** Pre-Security  
> **Difficulty:** Easy  
> **Category:** Networking  
> **Completed:** 2026  
> **Room Link:** https://tryhackme.com/room/whatisnetworking

---

## Summary

Foundational networking concepts essential for all cybersecurity work. Covers what networks are, how devices communicate, IP and MAC addressing, how data moves, and why these concepts matter in security monitoring, packet analysis, and incident response. The vocabulary every IR/DFIR analyst needs to read logs and packet captures.

---

## Key Concepts Learned

A network is a group of connected devices that can communicate and share resources.
Examples:
Home network
Corporate network
The internet (largest network of networks)

Security relevance: Every cyber defense activity relies on understanding network communications.
Logs, alerts, packet captures, and SIEM investigations all revolve around network behavior.

- **IP address** — A logical address assigned to a device (e.g. `192.168.1.1`); used to route data to the correct destination
- **MAC address** — A hardware-level address burned into a network card; unique per device; used for communication within a local network
- **Ping / ICMP** — Sends a packet to a target and waits for a reply to test reachability and measure latency
- **Data flow** — How packets travel from source to destination through routers, switches, and protocols

---

## Notes & Walkthrough

### Task 1 — What is Networking?

A network allows devices to communicate and share resources. The internet is a massive network that connects billions of devices globally.

### Task 2 — What is the Internet?

The internet is made up of many interconnected networks. Data travels across these networks using standardized protocols.

### Task 3 — Identifying Devices on a Network

Two main identifiers:
- **IP address** — logical, can change (dynamic) or stay fixed (static). Format: `xxx.xxx.xxx.xxx`
- **MAC address** — physical, tied to the hardware. Format: `xx:xx:xx:xx:xx:xx` (6 pairs of hex)

```bash
# Check your IP address
ip a

# Check MAC address
ip link show
```

### Task 4 — Ping (ICMP)

Ping uses ICMP (Internet Control Message Protocol) to test connectivity between devices.

```bash
ping 192.168.1.1        # Ping a local device
ping google.com         # Ping a domain (also tests DNS)
ping -c 4 192.168.1.1  # Send exactly 4 packets
```

---

## Tools Used

| Tool | Command | Purpose |
|------|---------|---------|
| `ping` | `ping <target>` | Test device reachability via ICMP |
| `ip` | `ip a` | Display IP and network interface info |

---

## Takeaways

- Every DFIR investigation starts with network logs — understanding IP/MAC addressing is non-negotiable
- IP addresses identify devices logically; MAC addresses identify them at the hardware layer — both appear in security logs
- Ping is the simplest recon/connectivity tool; ICMP traffic can also be used maliciously (ping sweeps, ICMP tunneling)
- This room is the foundation for DNS, HTTP, and packet analysis rooms to come

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/whatisnetworking)
- [Cisco Networking Basics](https://www.cisco.com/c/en/us/solutions/enterprise-networks/what-is-computer-networking.html)
