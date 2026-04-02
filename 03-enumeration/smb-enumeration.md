# SMB Enumeration

## Objective
Enumerate the Windows victim machine through SMB to identify
users, shares, and potential attack vessels.

## Commands Run

### Initial SMB Access Attempt
```bash
smbclient -L //10.10.10.176
```
Result: NT_STATUS_DENIED -- Windows security working as intended.

### enum4linux-ng Full Enumeration
```bash
enum4linux-ng -A 10.10.10.176
```
Swiss army knife of SMB enumeration. Runs ~10 tools simultaneously.

Key findings:
- NetBIOS domain name and MAC address of target machine
- OS version and full build details
- Partial user account information exposed anonymously

### Authenticated Enumeration
```bash
enum4linux-ng -A 10.10.10.176 -u "Student" -p "Pass123"
```
Re-ran with Student credentials to reveal significantly
more system information than anonymous access.

### C Drive Access Attempt
```bash
smbclient //10.10.10.176/C$ -U "Student"%Pass123
```
Result: Access Denied -- Windows correctly enforcing least privilege.

### RPC Enumeration
```bash
rpcclient -U "Student%Pass123" 10.10.10.176
rpcclient -U "Student%Pass123" --option='client max protocol=SMB3' -c "enumdomusers" 10.10.10.176
```
Successfully connected via RPC. Enumerated domain users,
group memberships, and password policies even with a
low-privilege account.

### Unattend.xml Check
```cmd
dir /s /p C:\Windows\Panther\Unattend.xml
```
Checked for cleartext admin password left over from Windows
installation. Password field was empty -- not exploitable.

## Key Findings
| Tool | Result |
|------|--------|
| smbclient | Access denied on C$ -- least privilege enforced |
| enum4linux-ng | Users, OS info, NetBIOS data exposed |
| rpcclient | Successfully authenticated via SMB3 |
| Unattend.xml | Password deleted -- not exploitable |

## MITRE ATT&CK Mapping
| Technique | ID |
|-----------|-----|
| SMB Enumeration | T1021.002 |
| Account Discovery | T1087 |
| Password Policy Discovery | T1201 |
| Unsecured Credentials | T1552 |

## Takeaways
- Guest access blocked -- Windows security working correctly
- Student account exposed significantly more info than anonymous access
- RPC is a powerful enumeration vector even with low privilege
- Always check for unattend.xml -- commonly overlooked by admins
