# Project 1 — Network Reconnaissance with Nmap

## Objective
Perform network reconnaissance against a Windows Server 2022 target
using Nmap from a Kali Linux attacker machine in an isolated lab environment.

---

## Lab Setup
| Machine | OS | IP |
|---|---|---|
| Attacker | Kali Linux 2025.4 | 192.168.10.10 |
| Target | Windows Server 2022 | 192.168.10.20 |
| Network | VirtualBox Internal (labnet) | 192.168.10.0/24 |

---

## Tools Used
- VirtualBox — hypervisor for running both VMs
- Nmap — network scanner
- xfreerdp — RDP client for Linux
- tcpdump — live packet capture

---

## What I Did

### 1. Built the Lab Environment
- Installed Kali Linux and Windows Server 2022 as VMs in VirtualBox
- Configured two network adapters on each VM: NAT (internet) and Internal Network (labnet)
- Set static IPs on both machines so they persist across reboots
- Verified connectivity with ping between both machines

### 2. Performed Nmap Scans

**Default scan:**
```bash
nmap 192.168.10.20
```

**Version detection:**
```bash
nmap -sV 192.168.10.20
```

**OS fingerprinting:**
```bash
nmap -O 192.168.10.20
```

**Full port scan:**
```bash
nmap -p- 192.168.10.20
```

### 3. Findings
| Port | Service | Notes |
|---|---|---|
| 3389 | RDP (ms-wbt-server) | Remote Desktop enabled |
| 5985 | WinRM (wsman) | Windows Remote Management |

### 4. RDP Access
Successfully connected from Kali into Windows Server using xfreerdp:
```bash
xfreerdp /v:192.168.10.20 /u:Administrator /cert:ignore
```

### 5. Traffic Capture
Captured live network traffic on the lab interface during scanning:
```bash
sudo tcpdump -i eth1 -w capture.pcap
```
Observed TCP SYN packets being sent to each port during the Nmap scan,
confirming the three-way handshake process at the packet level.

---

## Key Takeaways
- Nmap sends TCP SYN packets to each port — open ports respond with SYN-ACK,
  closed ports respond with RST
- RDP (3389) and WinRM (5985) are common attack surfaces on Windows servers
- tcpdump allows real-time visibility into what the network actually looks like
  during an attack
