# Project 3 — Active Directory & Domain Controller Setup

## Objective
Deploy and configure a Windows Server 2022 Domain Controller, set up
Active Directory Domain Services, create Organizational Units and users,
and enforce a domain-wide security policy using Group Policy.

---

## Lab Setup

| Machine | OS | IP | Role |
|---|---|---|---|
| Domain Controller | Windows Server 2022 | 192.168.10.20 | DC / AD DS |
| Attacker/Analyst | Kali Linux 2025.4 | 192.168.10.10 | Attacker (future labs) |
| Network | VirtualBox Internal (labnet) | 192.168.10.0/24 | Isolated lab network |

---

## Tools Used

- Windows Server Manager — role installation and server management
- Active Directory Users and Computers (ADUC) — user and OU management
- Group Policy Management Editor — GPO creation and configuration

---

## What I Did

### 1. Installed the AD DS Role

Opened Server Manager → Add Roles and Features → selected
**Active Directory Domain Services** and **Group Policy Management**.

This installs the software needed to turn the server into a Domain Controller.
Without this role the server is just a regular Windows Server with no
domain functionality.

### 2. Promoted the Server to a Domain Controller

After installation, used the post-deployment configuration wizard to
promote the server. Selected **Add a new forest** and set the root
domain name to `lab.local`.

**What this means:** Promoting a server to a DC means it now holds the
Active Directory database and is responsible for authenticating every
user and computer that joins the domain. Before promotion it was just
a server. After promotion it is the authority for the entire lab.local domain.

### 3. Configured the Domain

- Forest functional level: Windows Server 2016
- DNS Server: enabled (automatically installed alongside AD DS)
- NetBIOS domain name: LAB
- DSRM password set for recovery access

**What DNS has to do with AD:** Active Directory depends entirely on DNS
to function. When a computer wants to find the Domain Controller it uses
DNS to locate it. This is why DNS was automatically installed alongside AD DS.

### 4. Created Organizational Units (OUs)

Right clicked `lab.local` → New → Organizational Unit

Created two OUs:
- `staff` — for regular employees
- `IT` — for IT department accounts

**What OUs are:** Organizational Units are like folders inside Active
Directory. They let you organize users and computers into groups so you
can apply different policies to different departments. For example the
IT OU could have admin privileges while the staff OU has restricted access.

### 5. Created Domain User Accounts

Inside the `staff` OU created:
- **John Smith** — username: `jsmith`

Inside the `IT` OU created:
- **Jane Doe** — username: `jdoe`

Password settings:
- Password never expires (lab environment)
- Complexity requirements enforced by GPO

**What domain accounts mean:** These users can now log into any
domain-joined machine using their credentials. The Domain Controller
handles authentication using the Kerberos protocol — it issues a ticket
to prove identity without sending the password across the network.

### 6. Created and Configured a Group Policy Object (GPO)

Opened Group Policy Management → right clicked `lab.local` →
Created a GPO named **Lab Security Policy** → linked it to the domain.

Edited the GPO and configured the following under:
`Computer Configuration → Policies → Windows Settings → Security Settings
→ Account Policies → Password Policy`

| Policy | Setting |
|---|---|
| Minimum password length | 8 characters |
| Maximum password age | 90 days |
| Minimum password age | 30 days |
| Password must meet complexity requirements | Enabled |

**What GPO does:** A Group Policy Object is a rulebook that
automatically applies to every computer and user in the domain.
Instead of configuring each machine individually, you set the rule
once and it pushes to every domain-joined machine automatically.
This is how enterprise IT departments manage hundreds of machines at once.

---

## Key Takeaways

- A Domain Controller is the central authority for authentication
  in a Windows environment — every login goes through it
- Active Directory uses Kerberos for authentication — passwords are
  never sent across the network in plaintext
- Organizational Units allow granular control over different
  departments and user groups
- Group Policy is one of the most powerful tools in Windows
  administration — one change propagates to the entire domain
- This setup mirrors what 90% of real corporate environments run —
  making it directly applicable to SOC and Help Desk roles
