# Lab Setup

## Architecture
- OPNsense Firewall: 10.10.10.1
- Kali Linux Attacker: 10.10.10.126
- Windows Victim: 10.10.10.176

## OPNsense
- Subnet: 10.10.10.0/24
- WAN: em0 (Bridged to home network)
- LAN: em1 (Internal lab network)
- File system: ZFS

## Kali Linux
- 80GB storage
- Debian 64-bit
- eth0: 10.10.10.126

## Windows VM
- Two accounts configured:
  - user_victim (low privilege)
  - Student (low privilege, used for enumeration testing)
