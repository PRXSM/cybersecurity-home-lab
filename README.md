# 🛡️ Cybersecurity Home Lab

A hands-on cybersecurity home lab built from scratch using VirtualBox.
This lab simulates a real attacker/defender environment for practicing
offensive and defensive security techniques.

---

## 🖥️ Lab Environment

| Component | Details |
|---|---|
| Host Machine | Windows (i7, RTX 4070Ti, 16GB RAM) |
| Hypervisor | VirtualBox |
| Attacker/Analyst | Kali Linux 2025.4 — 192.168.10.10 |
| Target | Windows Server 2022 — 192.168.10.20 |
| Network | Internal Network (labnet) — 192.168.10.0/24 |

Both machines are isolated on an internal network with no external exposure.

---

## ✅ Completed Projects

### [Project 1 — Network Reconnaissance with Nmap](./project-1-nmap/)
- Built the full lab environment from scratch
- Configured static IPs and internal networking
- Performed Nmap scans: default, version detection, OS fingerprinting, full port
- Identified open ports: 3389 (RDP) and 5985 (WinRM)
- Successfully RDP'd from Kali into Windows Server using xfreerdp
- Captured live traffic with tcpdump and analyzed the TCP three-way handshake

### [Project 2 — Intrusion Detection with Snort IDS](./project-2-snort/)
- Installed and configured Snort 3 on Kali Linux
- Wrote custom detection rules for ICMP ping and TCP port scan detection
- Configured snort.lua for full IDS functionality
- Verified alerts firing correctly against real Nmap and ping traffic

---

## 🔜 Upcoming Projects

| # | Project | Description |
|---|---|---|
| 3 | Wireshark Traffic Analysis | Capture and analyze pcap files from lab traffic |
| 4 | Cisco Packet Tracer | Network simulation and routing/switching labs |
| 5 | Active Directory + Domain Controller | AD DS, users, GPOs, domain join |

---

## 🎯 Goals

- Build a portfolio of documented, hands-on security projects
- Support Security+ certification studies
- Develop practical skills for SOC Tier 1 / Help Desk roles
