# Adversary Emulation Cyber Range

A personal cyber range built to simulate real-world attack chains 
and practice threat detection and incident response. 
Continuously updated as the project evolves.

## Lab Architecture

| Role | Machine | IP |
|------|---------|-----|
| Firewall / Router | OPNsense | 10.10.10.1 |
| Attacker | Kali Linux | 10.10.10.126 |
| Victim | Windows VM | 10.10.10.176 |

## Project Roadmap

### Phase 1 -- Initial Attack Chain (Active)
Simulate a complete Living off the Land (LotL) attack 
from initial reconnaissance to reverse shell and 
privilege enumeration.

**Attack Chain:**
1. Reconnaissance -- Nmap port scanning and SMB enumeration
2. Payload Delivery -- Phishing email with malicious LNK/BAT 
   file delivered via Python HTTP server
3. Execution -- Base64 encoded PowerShell reverse shell 
   bypassing email and AV filters
4. C2 -- Netcat listener on Kali (port 4444)
5. Post-Exploitation -- Privilege enumeration via whoami /priv, 
   user enumeration via enum4linux-ng and rpcclient

**MITRE ATT&CK Mapping:**

| Technique | ID |
|-----------|-----|
| Phishing | T1566 |
| PowerShell Execution | T1059.001 |
| Obfuscated Files / Encoding | T1027 |
| Ingress Tool Transfer | T1105 |
| Reverse Shell / C2 | T1071 |
| Privilege Enumeration | T1069 |
| SMB Enumeration | T1021.002 |

---

### Phase 2 -- Detection and Defense 
Install OpenEDR  on the 
Windows VM and replay the Phase 1 attack chain to observe 
how the sensor detects and flags each technique. (Currently using OpenEDR until Falcon EDR sensor can be setup)

**Defense Chain:**
1. Deploy EDR sensor on Windows victim VM
2. Re-run the full Phase 1 attack chain
3. Analyze generated alerts and process trees in the EDR console
4. Triage alerts and document findings per technique
5. Write firewall rules in OPNsense to block attack vectors
6. Tune detection rules to reduce false positives
7. Write incident response report documenting the full attack 
   and defense lifecycle


### Phase 3 -- Expanding the Attack Surface
Build on Phase 1 and 2 with additional attack techniques 
and more advanced defense strategies.

**Planned attack techniques:**
- Active Directory enumeration and Kerberoasting
- Lateral movement via Pass the Hash
- Credential dumping
- Persistence mechanisms

**Planned defense techniques:**
- SIEM integration (Splunk or ELK)
- Custom detection rules mapped to MITRE ATT&CK
- Log correlation across endpoint, network and firewall

---

### Phase 4 -- AI Driven Red vs Blue (Planned)
Deploy an AI agent (OpenClaw architecture with custom 
MCP servers) to autonomously emulate adversary behavior 
against the lab environment while the blue team defends 
in real time.

**Goal:** Build a continuous feedback loop where attack 
and defense evolve together, simulating how real adversaries 
adapt to defender countermeasures.

---

## Tools Used

| Category | Tools |
|----------|-------|
| Virtualization | VirtualBox, OPNsense |
| Attacker OS | Kali Linux |
| Victim OS | Windows 10 |
| Reconnaissance | Nmap, enum4linux-ng, rpcclient, crackmapexec |
| Exploitation | smbclient, Netcat, PowerShell |
| Network Analysis | Wireshark |
| EDR (Phase 2) | Wazuh / LimaCharlie (Falcon when available) |
| SIEM (Phase 3) | Splunk / ELK |

## Write-Ups
Detailed write-ups for each phase will be added as 
the project progresses.
