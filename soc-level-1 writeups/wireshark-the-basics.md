# Wireshark: The Basics — TryHackMe

> **Path:** SOC Level 1 (SEC1) — Network Security & Traffic Analysis  
> **Difficulty:** Easy  
> **Category:** Networking / SIEM / Forensics  
> **Completed:** 2026-04-30  
> **Room Link:** https://tryhackme.com/room/wiresharkthebasics

---

## Summary

Hands-on introduction to Wireshark — the open-source packet analyzer that is to defenders what a stethoscope is to a doctor. This room covers GUI orientation, packet dissection across OSI layers, packet navigation, marking and commenting, object export, expert info anomaly detection, and display filters. It establishes the foundation every later traffic-analysis room and PCAP investigation will assume you have.

---

## Key Concepts Learned

- **PCAP (packet capture)** — the practice of intercepting network traffic and storing it for analysis; the resulting `.pcap` / `.pcapng` files are the bedrock evidence type in network forensics
- **Wireshark's three panes** — packet list (top), packet details (middle, layered tree), packet bytes (bottom, hex/ASCII)
- **Packet dissection** — Wireshark decodes each packet into its OSI layers as an expandable tree, making protocol structure directly observable
- **Capture filters vs display filters** — capture filters drop traffic at collection time and can't be undone; display filters hide traffic for viewing without losing the underlying packets
- **Follow Stream** — reassembles a TCP/UDP conversation across many packets into a readable transcript
- **Expert Info** — Wireshark's automatic anomaly detection that surfaces warnings, errors, and suspicious protocol states
- **Object export** — extracting transferred files (HTTP, SMB, TFTP, etc.) from a PCAP for further investigation

---

## Notes & Walkthrough

### Task 1 — What is Wireshark and what is it for?

Wireshark is a cross-platform packet analyzer that does three things, all of which matter for a SOC analyst:

1. **Detecting and troubleshooting network problems** — congestion, failure points, broken handshakes
2. **Detecting security anomalies** — rogue hosts, abnormal port usage, suspicious traffic patterns
3. **Investigating protocol details** — response codes, payload data, application-level forensics

**Important: Wireshark is not an IDS.** It doesn't decide what's malicious — it just lets you see traffic in detail. Detection depends entirely on the analyst's knowledge.

### Task 2 — GUI overview

The GUI has five distinct sections that an analyst uses constantly:

| Control | Purpose |
|---------|---------|
| Blue shark fin | Start sniffing (live capture) |
| Red square | Stop sniffing |
| Green refresh | Restart sniffing |
| Status bar | Active interface name + packet count |
| File → Merge | Combine two `.pcap` files into one |

### Task 3 — File details and provenance

Knowing **where a PCAP came from** is as important as knowing what's inside it. `Statistics → Capture File Properties` shows:

- File hash (integrity verification)
- Capture time
- Capture file comments
- Interface used for capture
- Packet count statistics

This metadata matters for chain-of-custody in real investigations. A PCAP without provenance is evidence of unknown origin.

### Task 4 — Packet dissection across OSI layers

When you click on any packet, Wireshark breaks it into a tree showing each OSI layer. Looking at an HTTP packet (packet #27 in the sample capture) reveals up to seven distinct layers:

| Wireshark label | OSI Layer | What it shows |
|-----------------|-----------|---------------|
| **Frame** | Layer 1 (Physical) | Frame number, capture interface, timing |
| **Source [MAC]** | Layer 2 (Data Link) | Source and destination MAC addresses (Ethernet II) |
| **Source [IP]** | Layer 3 (Network) | Source and destination IPv4 addresses |
| **Protocol** | Layer 4 (Transport) | TCP/UDP, source and destination ports |
| **Protocol Errors** | Layer 4 cont. | TCP segments needing reassembly |
| **Application Protocol** | Layer 5/7 (App) | HTTP, FTP, SMB, etc. — protocol-specific fields |
| **Application Data** | Layer 5/7 cont. | Actual payload data |

**This is the OSI model becoming concrete.** Every layer is right there, expandable, with real values you can read.

### Task 5 — Packet navigation

Several features for moving through large captures:

| Feature | Purpose |
|---------|---------|
| **Packet numbers** | Each packet gets a unique sequential ID — quote them in writeups |
| **Go to Packet** (`Ctrl+G`) | Jump to a specific packet number; also navigates within a TCP stream |
| **Find Packet** (`Ctrl+F`) | Search by Display filter, Hex value, String, or Regex across the three panes |
| **Mark Packet** | Flag a packet of interest for later review |
| **Packet Comments** | Annotate packets directly inside the PCAP file |

The Find function accepts four input types — String and Regex are the most useful for investigation work (e.g., searching all packets for the substring `password` or a specific username).

### Task 6 — Packet manipulation and export

| Feature | What it does | When you'd use it |
|---------|--------------|-------------------|
| **Export Packets** | Extract a subset of packets to a new PCAP | Sharing only suspicious packets with another team |
| **Export Objects** | Extract files transferred over the wire (HTTP, SMB, IMF, TFTP, DICOM) | Recovering attacker tools, exfiltrated documents, malware samples |
| **Time Display Format** | Toggle between "Seconds since capture" and UTC | Correlating with other log sources during incident reconstruction |

**Export Objects is one of the most powerful DFIR features in Wireshark** — when an attacker downloads a file or exfiltrates data over an unencrypted channel, you can reconstruct the file directly from the packets.

### Task 7 — Expert Info

Wireshark watches for protocol anomalies and surfaces them automatically. Found at `Analyze → Expert Information` or the bottom-left status bar.

Common categories:
- **Warnings** — unusual but not necessarily malicious (e.g., out-of-order TCP segments)
- **Errors** — protocol violations
- **Notes** — connection states (e.g., new connection, connection reset)

For an analyst, Expert Info is the equivalent of a SIEM's correlation engine, but at the packet level. It's not detection on its own, but it points your eye at things worth investigating.

### Task 8 — Filtering: capture vs display

Two fundamentally different filter types:

| Filter Type | When applied | Effect | Reversibility |
|-------------|--------------|--------|---------------|
| **Capture filter** | At capture time | Packets not matching are **never captured** | Irreversible — lost packets are lost |
| **Display filter** | After capture | Packets not matching are **hidden but still present** | Fully reversible — clear filter to see all |

**Practical rule:** capture filters in production environments where storage matters; display filters everywhere else. Default to display filters for analysis work.

### Task 9 — Display filter techniques (right-click menus)

Wireshark's golden rule: *"If you can click on it, you can filter on it."*

| Action | Where to find | What it does |
|--------|--------------|--------------|
| **Apply as Filter** | Right-click a field | Filters traffic to only that field's value |
| **Conversation Filter** | Right-click → Conversation Filter | Shows only the conversation between two endpoints |
| **Colourise Conversation** | Right-click → Colourise | Visually marks one conversation in the packet list |
| **Prepare as Filter** | Right-click → Prepare | Builds the filter expression without applying it (good for editing) |
| **Apply as Column** | Right-click a field → Apply as Column | Adds the field as a permanent column in the packet list |
| **Follow Stream** | Right-click → Follow → TCP/UDP/HTTP | Reconstructs the conversation at application level |

### Task 10 — Display filter queries

For when right-clicking isn't enough, use the green filter bar at the top.

**Filter by protocol name:**

```
http
dns
arp
tcp
udp
```

**Filter by port:**

```
tcp.port == 80
udp.port == 53
tcp.port == 443
```

**Filter by IP address:**

```
ip.addr == 192.168.1.2
ip.src == 10.0.0.5
ip.dst == 8.8.8.8
```

**Common useful filters:**

| Filter | Shows |
|--------|-------|
| `tcp.flags.syn == 1 and tcp.flags.ack == 0` | SYN-only packets — port scan signature |
| `tcp.flags.reset == 1` | RST packets — connection resets, scan responses |
| `http.request.method == "GET"` | All HTTP GET requests |
| `dns.qry.name contains "example"` | DNS queries containing "example" |
| `frame contains "password"` | Any packet whose payload contains the string "password" |

---

## Tools Used

| Tool | Command / Action | Purpose |
|------|------------------|---------|
| Wireshark | GUI | Packet capture and analysis |
| Wireshark filter bar | `ip.addr == X` etc. | Display filters to narrow traffic |
| Statistics → Capture File Properties | Menu | View file metadata, hash, comments |
| Analyze → Expert Information | Menu | Automatic anomaly surface |
| File → Export Objects | Menu | Extract files transferred in the capture |
| Right-click → Follow → TCP Stream | Right-click | Reassemble conversations |

---

## Takeaways

- **PCAP analysis is the most direct form of network forensics there is** — every other detection layer (SIEM rules, EDR alerts, firewall logs) is an abstraction over what Wireshark shows directly
- **The OSI model stops being theory the moment you click a packet** — Wireshark's expandable layer tree is the same diagram from textbooks, but with real values you can read and filter on
- **Capture filters destroy evidence; display filters preserve it** — for investigation work, default to display filters so nothing is lost
- **Expert Info is the analyst's first-pass triage tool** — when handed an unfamiliar PCAP, opening Expert Info before scrolling through packets surfaces the most likely points of interest
- **Export Objects is the bridge from "I see traffic" to "I have evidence"** — when an attacker transfers a file unencrypted, this feature reconstructs it for you, hash-verified
- **The right-click menu is your fluency multiplier** — every right-click action ("Apply as Filter", "Follow Stream") teaches you a query you'd otherwise have to memorize; over time, you stop right-clicking and just type the filter
- **For IR/DFIR specifically:** PCAP analysis appears in nearly every blue-team certification (Sec+, CDSA, eCIR), most SOC interview scenarios, and CyberDefenders/HTB Sherlocks challenges. Fluency here compounds across the whole career path

---

## References

- [Official TryHackMe Room](https://tryhackme.com/room/wiresharkthebasics)
- [Wireshark Display Filter Reference](https://www.wireshark.org/docs/dfref/)
- [Wireshark User Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [MITRE ATT&CK — Network Sniffing (T1040)](https://attack.mitre.org/techniques/T1040/)
- [MITRE ATT&CK — Application Layer Protocol (T1071)](https://attack.mitre.org/techniques/T1071/)
- Companion: TryHackMe — [Packets & Frames](https://github.com/egonzalez071679/tryhackme-writeups/blob/main/pre-security%20writeups/packets-and-frames.md), [OSI Model](https://github.com/egonzalez071679/tryhackme-writeups/blob/main/pre-security%20writeups/osi-model.md)
