# Post-Exploitation -- Privilege Enumeration

## Objective
After gaining reverse shell access via the Student account,
enumerate privileges and understand what damage a low-level
compromised account can do in a real enterprise environment.

## Context
In a real penetration test, the goal is not just to get in.
It is to show the client exactly how much damage a phished
employee or compromised low-privilege account can cause.
This phase answers that question.

## Commands Run

### Identify Current User
```cmd
whoami
whoami /priv
```
Shows the current user and every privilege assigned to
the account. Attackers look for dangerous privileges
like SeImpersonatePrivilege which can be abused to
escalate all the way to SYSTEM.

### Check C Drive Access
```bash
smbclient //10.10.10.176/C$ -U "Student"%Pass123
```
Result: Access Denied -- Windows correctly enforcing
least privilege. Student cannot access the C drive.

### RPC User Enumeration
```bash
rpcclient -U "Student%Pass123" 10.10.10.176
```
Then inside rpcclient:
```bash
enumdomusers
queryuser Student
```
Pulls the full user list, group memberships, last login
times, password set dates, and failed login attempts
even from a low privilege account.

### Check for Unattend.xml
```cmd
dir /s /p C:\Windows\Panther\Unattend.xml
```
Checks for leftover Windows installation files that
sometimes contain cleartext admin passwords. Password
field was empty -- not exploitable in this case.

## Key Findings

| Check | Result | Impact |
|-------|--------|--------|
| whoami /priv | Limited privileges | Low direct damage |
| C drive access | Denied | Least privilege enforced |
| User enumeration | Full user list exposed | Usernames = 50% of a breach |
| Unattend.xml | Password deleted | Not exploitable |
| Admin status | Not admin | Privilege escalation needed |

## Key Takeaway
Even a low-privilege Student account exposed more
information than it should in a real enterprise --
full user lists, OS details, group memberships, and
password policies. This is why attackers target
phished employees. They do not need admin access to
gather intelligence. They just need a foot in the door.

## Next Steps -- Phase 2
- Install EDR sensor on Windows VM
- Re-run the full attack chain
- Monitor and triage detections in the EDR console
- Document the defensive findings per technique
- Write firewall rules in OPNsense to block attack vectors

## Project Branch Off -- Phase 3
- Build an OpenClaw AI agent using the Anthropic API
  with custom MCP servers to autonomously generate and
  execute attack chains against the lab environment
- Agent will adapt its tactics based on what evades
  detection, mapping behavior to MITRE ATT&CK techniques
  in real time
- Blue team defense layer will monitor and respond via
  EDR and SIEM simultaneously
- Goal: continuous red vs blue feedback loop where attack
  and defense evolve together autonomously

## MITRE ATT&CK Mapping
| Technique | ID |
|-----------|-----|
| Account Discovery | T1087 |
| Permission Groups Discovery | T1069 |
| Unsecured Credentials | T1552 |
| OS Credential Dumping (planned) | T1003 |
| Privilege Escalation (planned) | T1068 |
