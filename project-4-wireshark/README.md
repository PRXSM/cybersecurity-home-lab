# Project 4 — Wireshark Traffic Analysis

## Objective
Capture and analyze live network traffic between Kali Linux and Windows Server 2022 using Wireshark. Identify and interpret ICMP, TCP port scan, and RDP traffic at the packet level.

---

## Lab Setup

| Machine | OS | IP |
|---|---|---|
| Attacker/Analyst | Kali Linux 2025.4 | 192.168.10.10 |
| Target | Windows Server 2022 | 192.168.10.20 |
| Network | VirtualBox Internal (labnet) | 192.168.10.0/24 |

---

## Tools Used

- Wireshark 4.6.4 — GUI packet analyzer
- ping — ICMP traffic generation
- Nmap — TCP port scan traffic generation
- xfreerdp — RDP connection traffic generation

---

## What I Did

### 1. Launched Wireshark and Started Capturing

Launched Wireshark on Kali and selected the `eth1` interface — the internal lab network adapter where all traffic between Kali and Win Server flows.

`sudo wireshark`

Started a live capture on eth1 and observed that a quiet network produces no packets — Wireshark only captures traffic when something is actually communicating.

### 2. Generated and Captured ICMP Traffic

In a second terminal ran a ping against Win Server:

`ping 192.168.10.20 -c 4`

Wireshark immediately captured the ICMP packets. Applied the filter:

`icmp`

**What I observed:** Four ICMP Echo Request packets from Kali and four ICMP Echo Reply packets from Win Server — the classic ping request/reply pattern. Each packet showed all four OSI layers:

- Frame — physical layer data, packet size and timing
- Ethernet II — Layer 2, MAC addresses of both machines
- Internet Protocol v4 — Layer 3, IP addresses 192.168.10.10 and 192.168.10.20
- ICMP — Layer 4, ping request type and sequence number

This was the OSI model visible in real captured traffic — not a textbook diagram but actual packets showing each layer stacked on top of the other.

### 3. Generated and Captured TCP Port Scan Traffic

Ran an Nmap scan against Win Server:

`nmap 192.168.10.20`

Applied the Wireshark filter to show only SYN packets:

`tcp.flags.syn == 1 && tcp.flags.ack == 0`

**What I observed:** Hundreds of TCP SYN packets sent from Kali to Win Server — each one targeting a different destination port. This is the exact signature of a port scan. Nmap sends a SYN packet to each port asking if it is open, without completing the full three-way handshake.

This is the same traffic that the custom Snort rule in Project 2 was designed to detect — the `flags:S` rule catches exactly these SYN-only packets. Seeing the traffic visually in Wireshark confirmed what the Snort alerts were reporting.

### 4. Generated and Captured RDP Traffic

Connected from Kali to Win Server via RDP:

`xfreerdp /v:192.168.10.20 /u:Administrator /cert:ignore`

Applied the Wireshark filter:

`tcp.port == 3389`

**What I observed:** The RDP handshake packets showing TPKT and X.224 protocols — the initial negotiation between client and server. After the handshake the session traffic is fully encrypted so Wireshark shows only encrypted application data — the actual desktop content cannot be read.

This is significant from a security perspective — while RDP encrypts its content, the fact that port 3389 is open and receiving connections is fully visible to anyone monitoring the network. In real environments RDP should be restricted to VPN access only or placed behind a firewall.

### 5. Saved the Capture File

Saved the full packet capture for future analysis:

`/home/kali/lab-capture-1.pcap`

---

## Key Takeaways

- Wireshark makes network traffic visible and readable at every OSI layer
- A quiet network produces zero packets — Wireshark only captures what is actually happening
- ICMP ping traffic has a clear request/reply pattern that is easy to identify and filter
- TCP SYN packets are the signature of port scanning — the same pattern Snort detects with the `flags:S` rule
- RDP encrypts its content but its presence on port 3389 is fully visible — making firewall rules and VPN access critical
- Pcap files can be saved and reopened for later analysis — this is how SOC analysts investigate incidents after the fact
