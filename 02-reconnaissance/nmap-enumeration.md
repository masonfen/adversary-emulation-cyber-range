# Reconnaissance -- Nmap Enumeration

## Objective
Identify open ports and services on the Windows victim 
machine from the Kali attacker machine.

## Commands Run

### Initial Host Discovery
```bash
sudo nmap -sn 10.10.10.0/24
```
Scans the entire subnet to find live hosts.

### Service and OS Detection
```bash
sudo nmap -sV -sC -O -v 10.10.10.176
```
| Flag | Purpose |
|------|---------|
| -sV | Detects service versions |
| -sC | Runs default safe scripts |
| -O | OS detection |
| -v | Verbose output |

### Targeted Port Scan
```bash
sudo nmap -Pn -p 445,135,139,3389 --reason 10.10.10.176
```
Focused on top Windows ports. 445 is SMB -- the primary target.

### EternalBlue Vulnerability Check
```bash
nmap -p 445 --script smb-vuln-ms17-010 10.10.10.176
```
Checked for MS17-010 vulnerability. Host not vulnerable.

## Key Findings
| Port | Service | Significance |
|------|---------|-------------|
| 445 | SMB | Primary attack vector |
| 135 | RPC | Discovery and enumeration |
| 139 | NetBIOS | Legacy file sharing |

## MITRE ATT&CK
- T1046 -- Network Service Scanning
- T1135 -- Network Share Discovery

## Takeaways
- Ports 135, 139, 445 all open
- SYN-ACK response confirms open ports
- SMB is the primary attack surface
