# Project 2 — Intrusion Detection with Snort IDS

## Objective
Deploy and configure Snort 3 as an Intrusion Detection System (IDS) on Kali Linux to detect real attack traffic generated against a Windows Server 2022 target.

---

## Lab Setup

| Machine | OS | IP |
|---|---|---|
| Attacker/Analyst | Kali Linux 2025.4 | 192.168.10.10 |
| Target | Windows Server 2022 | 192.168.10.20 |
| Network | VirtualBox Internal (labnet) | 192.168.10.0/24 |

---

## Tools Used

- Snort 3 — open source intrusion detection system
- Nmap — used to generate port scan traffic for detection testing
- ping — used to generate ICMP traffic for detection testing

---

## What I Did

### 1. Installed Snort 3

`sudo apt install snort -y`

`snort --version`

### 2. Wrote Custom Detection Rules

Created a local rules file at `/etc/snort/rules/local.rules`

`alert icmp any any -> 192.168.10.0/24 any (msg:"ICMP Ping Detected"; sid:1000001; rev:1;)`

`alert tcp any any -> 192.168.10.20 any (msg:"TCP Port Scan Detected"; flags:S; sid:1000002; rev:1;)`

**Rule breakdown:**
- Rule 1 — alerts on any ICMP traffic destined for the lab network
- Rule 2 — alerts on TCP SYN packets destined for Win Server, which is the signature of a port scan

### 3. Configured Snort

Added the following blocks to `/etc/snort/snort.lua`:

`ips = { enable_builtin_rules = true, include = '/etc/snort/rules/local.rules', }`

`alert_fast = { file = true, packet = false, }`

This tells Snort to load our custom rules and write alerts to a file.

### 4. Started Snort

`sudo snort -i eth1 -c /etc/snort/snort.lua -l /var/log/snort`

### 5. Generated Attack Traffic

`ping 192.168.10.20 -c 4`

`nmap 192.168.10.20`

### 6. Verified Alerts

`sudo cat /var/log/snort/alert_fast.txt`

---

## Results

**ICMP Detection — ping generated 8 alerts (4 outbound, 4 inbound)**

`[1:1000001:1] "ICMP Ping Detected" {ICMP} 192.168.10.10 -> 192.168.10.20`

**TCP Port Scan Detection — Nmap generated hundreds of SYN alerts**

`[1:1000002:1] "TCP Port Scan Detected" {TCP} 192.168.10.10 -> 192.168.10.20:3389`

`[1:1000002:1] "TCP Port Scan Detected" {TCP} 192.168.10.10 -> 192.168.10.20:5985`

Both custom rules fired correctly against real attack traffic. ✅

---

## Key Takeaways

- Snort detects threats by matching live traffic against rules in real time
- TCP SYN flag (`flags:S`) is the signature of port scanning behavior
- IDS systems like Snort are a core component of any SOC environment
- Writing custom rules gives analysts precise control over what gets alerted
